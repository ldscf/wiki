---
source_title: Apache2
categories:
- Develop
- Linux
- Web
last_modified: '2025-06-17T05:51:17Z'
---
![Apache-http.png](https://mwbbs.eu.org/wiki/images/7/78/Apache-http.png)

Apache HTTP Server（简称Apache）源于 NCSAhttpd 服务器，是 Apache 软件基金会的一个开放源码的网页服务器，是最流行的Web服务器端软件之一。快速、可靠并且可以简单的API扩展。是世界使用排名第一的 Web 服务器软件，市场占有率达60%左右。

Apache取自“a patchy server”的读音，意思是充满补丁的服务器，Apache的特点是简单、速度快、性能稳定，并可做代理服务器来使用。

本来它只用于小型或试验 Internet 网络，后来逐步扩充到各种 Unix 系统中，尤其对 Linux 的支持相当完美。Apache 有多种产品，可以支持SSL技术，支持多个虚拟主机。

Apache 的诞生极富有戏剧性。当 NCSAWWW 服务器项目停顿后，那些使用 NCSAWWW 服务器的人们开始交换他们用于该服务器的补丁程序，他们也很快认识到成立管理这些补丁程序的论坛是必要的。就这样，诞生了 Apache Group，后来这个团体在 NCSA(National Center for Supercomputer Applications，国家超级计算机应用中心），美国最大的公共超级计算机机构，始建于1985年。) 的基础上创建了 Apache。

Apache 是以进程为基础的结构，进程要比线程消耗更多的系统开支，不太适合于多处理器环境，因此，在一个 Apache Web 站点扩容时，通常是增加服务器或扩充群集节点而不是增加处理器。

### Apache2 目录

*[default] /etc/apache2/*

### 增加子目录

*''/etc/apache2/sites-available/000-default.conf''*

     Alias /wiki "/u01/web/wiki/"

     Alias /soft "/u01/web/soft/"
     
       Options FollowSymlinks

       AllowOverride None

       Require all granted
     

### 为访问Apache2 目录及文件增加用户名密码验证

#### 建立用户密码文件

*''创建 -C 修改 -m''*
```
 htpasswd -c .htpasswd bi
```

#### 修改配置文件

*''/etc/apache2/sites-available/000-default.conf''*
```
 Alias /backup "/u01/web/backup/"
 
```

     Options Indexes FollowSymLinks

     AllowOverride None

     '''AuthType Basic

     AuthName Authorize

     AuthUserFile /u01/web/backup/.htpasswd

     Require user bi
```
 
```

### 修改端口

*''/etc/apache2/sites-available/000-default.conf''*
```
 
 
```

*''/etc/apache2/ports.conf''*
```
 Listen 2080
```

### 编码

#### 默认编码
```
 # /etc/apache2/conf-available/charset.conf
 AddDefaultCharset UTF-8
```

#### 路径编码

指定某个路径的编码，可以使用 .htaccess 立即生效。
```
 AddDefaultCharset UTF-8
```

#### 特殊文件
```
 AddCharset UTF-8 .html .php .txt .md .json .csv
```

AddDefaultCharset UTF-8 的意思是：如果一个 html 格式文件的响应内容没有声明字符集（例如 index.html 中没有 Content-Type: text/plain; charset=UTF-8），那么 Apache 将自动添加 charset=UTF-8。而对于一些特殊的文件类型，Apache 默认并不声明 charset（即使有 AddDefaultCharset），浏览器可能误判为 ISO-8859-1，故而需要 AddCharset 指定。

P.S. 需要说明的是，考虑到浏览器缓存的因素，故而上面的修改，再访问时务必在本机清除已有缓存内容，或采用“无痕式”访问。
