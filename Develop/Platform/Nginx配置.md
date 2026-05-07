---
source_title: Nginx配置
categories:
- Develop
- Platform
- Web
last_modified: '2024-04-28T05:45:47Z'
---
Nginx 是一个高性能的 HTTP 和反向代理 web 服务器，同时也提供了 IMAP/POP3/SMTP 服务。Nginx 是由伊戈尔·赛索耶夫（[[wikipedia:Igor_Sysoev|Igor Sysoev]]）2002年在俄罗斯访问量第二的 Rambler.ru 站点（Рамблер）时开发的。

源代码以类BSD许可证的形式发布，因它的高并发、稳定性、低系统资源占用、丰富的功能集、简单的配置文件而闻名。

Nginx 可以根据不同的正则匹配，采取不同的转发策略，如图片文件结尾的走文件服务器，动态页面走 web 服务器，Nginx 可以对返回结果进行错误页跳转，异常判断等，且如果被分发的服务器存在异常，可以将请求重新转发给另外一台服务器，然后自动去除异常服务器。

Nginx 模块需要集成到内核中，不能动态加载。要包含非标准模块，用户必须从源代码编译。(Apache 具有动态加载模块的能力)

https://nginx.org/en/download.html

### 安装
1. 安装编译环境及基础包: yum install -y gcc gcc-c++ zlib zlib-devel gd gd-devel
1. 安装pcre软件包（使nginx支持http rewrite模块）: yum install -y pcre pcre-devel
1. 安装 openssl-devel（使 nginx 支持 ssl）: yum install -y openssl openssl-devel
1. 创建用户 nginx: useradd -s /sbin/nologin nginx
```
 ./configure --prefix=/usr/local/nginx \
 --user=nginx \
 --group=nginx \
 --with-pcre \
 --with-http_ssl_module \
 --with-http_v2_module \
 --with-http_realip_module \
 --with-http_addition_module \
 --with-http_sub_module \
 --with-http_dav_module \
 --with-http_flv_module \
 --with-http_mp4_module \
 --with-http_gunzip_module \
 --with-http_gzip_static_module \
 --with-http_random_index_module \
 --with-http_secure_link_module \
 --with-http_stub_status_module \
 --with-http_auth_request_module \
 --with-http_image_filter_module \
 --with-http_slice_module \
 --with-mail \
 --with-threads \
 --with-file-aio \
 --with-stream \
 --with-mail_ssl_module \
 --with-stream_ssl_module
```

with-http_realip_module 用于获取真实的客户端 IP 地址。
```
 make && make install
```
 
```
 nginx -V
```

#### 启动服务
```
 nginx              # 启动Nginx
 nginx -t           # 验证配置文件是正确
 nginx -s reload    # 重启Nginx
 nginx -s stop      # 停止Nginx
 nginx -v           # 查看是否安装成功
 nginx -s reopen    # 重新打开日志文件
 nginx -s quit      # 完整有序的停止nginx
```

### 代理
- 正向代理：局域网中的客户端通过代理服务器访问Internet
- 反向代理：客户端将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，再返回给客户端。反向代理服务器可以隐藏了真实服务器 IP地址

正向代理代理客户端，反向代理代理服务器。

### 负载均衡
- 内置策略：轮询、加权轮询、Ip hash
- 扩展策略

### web缓存

对不同的文件做不同的缓存处理，配置灵活，并且支持 FastCGI_Cache，主要用于对 FastCGI 的动态程序进行缓存。

代理服务器可以缓存原始资源服务器的资源，而不是每次都要向原始资源服务器请求数据，特别是一些静态的数据，比如图片和文件。如果代理服务器和客户端来自同一个网络（或一个城域网），那么客户端访问代理服务器，就会得到很高质量的速度——而这正是 CDN 技术的核心。

### 配置文件

Nginx 默认的配置文件是在安装目录的 conf 目录下，修改过 nginx.conf 配置文件，需要重启Nginx服务。

查看配置文件 nginx.conf 路径：**nginx -t**
```
 /etc/nginx/nginx.conf
 在 http 块中引用了：
 include /etc/nginx/sites-enabled/*;(--> 软链接：/etc/nginx/sites-available/default)
```

nginx.conf 配置文件分为三部分：

#### 全局块

从配置文件开始到 events 块之间的内容，主要会设置一些影响 Nginx 服务器整体运行的配置指令。主要包括：配置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。如：
```
 worker_processes    8;        # 并发设置，或使用：auto;
```

一般等于 grep processor /proc/cpuinfo | wc -l

#### events 块

events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括：是否开启对多 work process 下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 work process 可以同时支持的最大连接数等。如：

events {

   process          1024;     # 最大连接数，一般 512M 内存使用该配置

}

#### http 块

这部分是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。需要注意的是：http 块也可以包括 http 全局块、server 块。下面的反向代理、动静分离、负载均衡都是在这部分中配置。
- http 全局块：http 全局块配置的指令包括：文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等
- server 块：这块和虚拟主机有密切关系，从用户角度看，虚拟主机和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本

每个http块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

##### http: server 配置

###### listen
- listen  IP_address:port        #监听指定的地址和端口号，如果只定义了IP地址，没有定义端口号，那么就使用80端口
- listen  IP_address           #监听指定 ip 地址所有端口，如果是 IPV6 地址，需要使用中括号[] ，如[fe80::1]等
- listen  *:80 | *:8080         #监听所有 80 端口和 8080 端口，
- listen  80                 #监听该端口的所有IP连接

###### server_name

server_name   name name ...，多个名称，中间用空格隔开。
- server_name abc.com www.abc.com

可以使用通配符“*”，但通配符只能用在由三段字符组成的首段或者尾端，或者由两端字符组成的尾端
- server_name   *.abc.com www.abc.*
- 使用正则表达式，用“~”作为正则表达式字符串的开始标记

###### location

该指令用于匹配 URL

location [ PATTERN ] url {

}

**PATTERN**

#=   用于不含正则表达式的 uri 前，要求请求字符串与 url 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求
1. ~   用于表示 url 包含正则表达式，并且区分大小写
1. ~*  用于表示 url 包含正则表达式，并且不区分大小写
1. ^~  用于不含正则表达式的 url 前，要求 Nginx 服务器找到标识 url 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 块中的正则 url 和请求字符串做匹配
1. /a   普通前缀匹配，优先级低于带参数前缀匹配
1. /     任何没有匹配成功的，都会匹配这里处理

注意：如果 url 包含正则表达式，则必须要有 ~ 或者 ~* 标识。优先级从高到低。

**proxy_pass**

用于设置被代理服务器的地址。可以是主机名称、IP地址加端口号的形式

proxy_pass URL;

**index**

用于设置网站的默认首页

index  index.html index.php;
- 在请求访问网站时，请求地址可以不写首页名称
- 可以对一个请求，根据请求内容而设置不同的首页

**rewrite**

URL 重写

rewrite ^/(.*) http://www.abc.tk/wiki/$1 permanent;

rewrite            [flag];
1. regex：perl 兼容正则表达式
1. replacement：将正则匹配的内容替换
1. flag：标记
- last  匹配完成后继续，向下匹配新的 location URI 规则
- break 匹配完成终止，不再匹配后面的任何规则
- redirect  返回 302 临时重定向，显示跳转后的 URL 地址
- permanent  返回 301 永久重定向，显示跳转后的 URL 地址

应用场景
- 调整用户浏览的 URL，看起来更规范
- 让搜索引擎搜录网站内容及用户体验更好，会将动态 URL 地址伪装成静态地址
- 将旧的访问域名跳转到新的域名上
- 根据特殊变量、目录、客户端的信息进行URL调整

### Example

#### 端口隐藏

访问 http://www.abc.tk，实际访问 http://127.0.0.1:8080
```
 server {
```

   listen   80;

   server_name www.abc.tk;
 
   location / {

     proxy_pass http://127.0.0.1:8080/;

     index index.html index.htm index.php;

   }
```
 }
```

这段配置一般情况下都正常，但偶尔会出错——服务器给客户端的跳转指令里加了端口号，如 http://127.0.0.1:8080/index.html。

因为 nginx 服务器侦听的是 80 端口，所以这样的 URL 给了客户端,必然会出错。

针对这种情况，加一条 proxy_redirect 指令重定向 url: 

proxy_redirect http://127.0.0.1:8080/ /;

即把所有 “http://127.0.0.1:8080/” 的内容替换成 “/” 再发给客户端。

#### 隐藏式跳转

访问 http://www.mkwiki.tk，以及http://mwwiki.eu.org，实际访问 http://www.mwbbs.tk/wiki/
```
 server {
```

     listen       80;

     server_name  www.mkwiki.tk mwwiki.eu.org;

     proxy_set_header Host $host:$server_port;
 
     location / {

         proxy_pass http://10.0.0.141:2080/wiki/;

         index index.html index.htm index.php;

     }
```
 }
```

#### 路径匹配
```
 ## BBS: Milky Way BBS
 # 虽然 / 放前面，但执行的时候并不是按照这个顺序，而是先“5./wiki/ 普通前缀匹配”，最后匹配 “6./”
```
 
```
 server {
```

     listen       80;

     server_name  mwbbs.tk www.mwbbs.tk mwbbs.eu.org;
 
     location / {

         proxy_pass http://10.0.0.141:2080/mwbbs/;

         index  index.html index.htm index.php;

     }
     
     location /wiki/ {

         proxy_pass http://10.0.0.141:2080/wiki/;

         #proxy_redirect off;

         index  index.html index.htm index.php;

     }
 
```
 }
```

显然，如果在目标端(如上面 10.0.0.141)配置了逻辑目录，如在 apache2（000-default.conf）配置了 

    Alias /dept  "/u01/web/dept/"

    Alias /doc   "/u01/web/doc/"

那么在 Nginx 中也应该配置同样目录：

     location /dept/ {

         proxy_pass http://10.0.0.141:2080/dept/;

         #proxy_redirect off;

         index  index.html index.htm index.php;

     }

     location /doc/ {

         proxy_pass http://10.0.0.141:2080/doc/;

         #proxy_redirect off;

         index  index.html index.htm index.php;

     }
```
 在配置proxy_pass代理转发时，如果后面的 url 加 /，表示绝对根路径；如果没有 /，表示相对路径。如：
```
 
     location /dept/ {

         proxy_pass http://10.0.0.141:2080;

         #proxy_redirect off;

         index  index.html index.htm index.php;

     }

     location /doc   / {

         proxy_pass http://10.0.0.141:2080;

         #proxy_redirect off;

         index  index.html index.htm index.php;

     }

#### 路径隐藏

nginx 在转发时移除上下文(即去掉匹配路径)，如去掉：mwbbs.eu.org/bbs/index.php 中的 bbs/：

    location ^~ /bbs/ {

        rewrite ^/bbs/(.*)$ /$1 permanent;

    }

#### 显式跳转

访问 http://www.mkwiki.tk，以及http://mwwiki.eu.org，实际地址跳转为 http://www.mwbbs.tk/wiki/
```
 server {
```

     listen       80;

     server_name  wiki.mwbbs.tk mwwiki.eu.org;
 
     location / {

         rewrite ^/(.*) http://www.mwbbs.tk/wiki/$1 permanent;

         index  index.html index.htm index.php;

     }
```
 }
```

#### 真实客户端 IP
```
 server {
```

   listen   80;

   server_name mwbbs.eu.org;
 
   location / {

     proxy_pass http://127.0.0.1:8080/;

     index index.html index.htm index.php;

     proxy_set_header x-real-ip $remote_addr;

     #proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

     #real_ip_header   X-Forwarded-For;

     #real_ip_recursive on;

   }
```
 }
```
 ```
<!DOCTYPE html>
 
 
 
 TEST-realip
 <?php
   echo '1.'.$_SERVER['HTTP_REFERER'].'';
   echo '2.'.$_SERVER['HTTP_X_REAL_IP'].'';
   echo '3.'.$_SERVER['REMOTE_ADDR'].'';
   echo '4.'.$_SERVER['REAL_IP_HEADER'].'';
   echo '5.'.$_SERVER['X-Forwarded-For'].'';
 ?>
 
 
 
```

### Error

#### 跳转 URL 带内网 IP/端口号的解决办法
```
 proxy_set_header Host $host:$server_port;
 # 一般而言，会用 $host 代替 $http_host 变量，从而避免 http 请求中丢失 Host 头部的情况下 Host 不被重写(一种可能的情况是跳转内网 IP 时，出现跳转不过去且显示出内网 IP 的情况)。
 proxy_set_header X-Forwarded-For $remote_addr;
 # X-Forwarded-For：简称 XFF 头，它代表客户端，也就是 HTTP 的请求端真实的 IP，只有在通过了 HTTP 代理或者负载均衡服务器时才会添加该项。
 # 这一HTTP头一般格式如下：X-Forwarded-For: client1, proxy1, proxy2。其中的值通过一个“逗号+空格”把多个IP地址区分开, 最左边(client1)是最原始客户端的IP地址, 代理服务器每成功收到一个请求，就把请求来源IP地址添加到右边。
```

### 参考
```
 # nginx.conf
 #
 # 全局参数设置 
 worker_processes  1;          # 设置nginx启动进程的数量，一般设置成与逻辑cpu数量相同 
 error_log  logs/error.log;    # 指定错误日志 
 worker_rlimit_nofile 10240;   # 设置一个nginx进程能打开的最大文件数 
 pid        /var/run/nginx.pid; 
 events {                      # 事件配置
```

     worker_connections  1024; # 设置一个进程的最大并发连接数

     use epoll;                # 事件驱动类型
```
 } 
 # http 服务相关设置 
 http {  
```

     log_format  main  'remote_addr - remote_user [time_local] "request" '

                       'status body_bytes_sent "$http_referer" '

                       '"http_user_agent" "http_x_forwarded_for"'; 

     access_log  /var/log/nginx/access.log  main;    #设置访问日志的位置和格式 

     sendfile          on;      # 用于开启文件高效传输模式，一般设置为on，若nginx是用来进行磁盘IO负载应用时，可以设置为off，降低系统负载

     tcp_nopush        on;      # 减少网络报文段数量，当有数据时，先别着急发送, 确保数据包已经装满数据, 避免了网络拥塞

     tcp_nodelay       on;      # 提高I/O性能，确保数据尽快发送, 提高可数据传输效率                           

     gzip              on;      # 是否开启 gzip 压缩 

     keepalive_timeout  65;     # 设置长连接的超时时间，请求完成之后还要保持连接多久，不是请求时间多久，目的是保持长连接，减少创建连接过程给系统 带来的性能损                                    耗，类似于线程池，数据库连接池

     types_hash_max_size 2048;  # 影响散列表的冲突率。types_hash_max_size  越大，就会消耗更多的内存，但散列key的冲突率会降低，检索速度就更快。                                             types_hash_max_size越小，消耗的内存就越小，但散列key的冲突率可能上升

     include             /etc/nginx/mime.types;  # 关联mime类型，关联资源的媒体类型(不同的媒体类型的打开方式)

     default_type        application/octet-stream;  # 根据文件的后缀来匹配相应的MIME类型，并写入Response  header，导致浏览器播放文件而不是下载
```
 # 虚拟服务器的相关设置 
```

     server { 

         listen      80;                # 设置监听的端口 

         server_name  localhost;        # 设置绑定的主机名、域名或ip地址 

         charset koi8-r;                # 设置编码字符 

         location / { 

             root  /var/www/nginx;           # 设置服务器默认网站的根目录位置 

             index  index.html index.htm;    # 设置默认打开的文档 

             } 

         error_page  500 502 503 504  /50x.html; # 设置错误信息返回页面 

             location = /50x.html { 

             root  html;        # 这里的绝对位置是/var/www/nginx/html 

         } 

     } 

  } 
