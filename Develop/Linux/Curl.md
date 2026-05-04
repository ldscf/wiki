---
source_title: Curl
categories:
- Develop
- Linux
last_modified: '2026-01-21T05:13:43Z'
---
curl 是一个在 Linux 中利用 URL 规则在命令行下工作的文件传输工具，是一款很强大的 http 命令行工具。支持文件的上传和下载，是综合传输工具，但按传统，习惯称curl 为下载工具。

#### Get
```
 curl http://127.0.0.1:8080/login?admin&passwd=12345678
```
 
```
 # 通过代理服务器访问
 curl -x socks5://127.0.0.1:1080 https://ifconfig.me
```
 
```
 # 如果本地 DNS 环境不稳定或被污染，可以使用 socks5h 将域名交给代理服务器去解析
 curl -x socks5h://127.0.0.1:1080 https://ifconfig.me
```

#### Post
```
 curl -d “user=admin&passwd=12345678” http://127.0.0.1:8080/login
```

#### Post Json
```
 curl -H “Content-Type:application/json” -X POST -d '{“user”: “admin”, “passwd”:“12345678”}' http://127.0.0.1:8000/login
```

#### 模仿浏览器

有些网站需要使用特定的浏览器去访问，有些还需要使用某些特定的版本。option: -A 可以指定浏览器
```
 curl -A "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.0)" http://www.bing.com
```

#### 伪造 referer（盗链）

有些服务器会检查 http 访问的 referer 从而来控制访问。

比如：你是先访问首页，然后再访问首页中的邮箱页面，这里访问邮箱的 referer 地址就是访问首页成功后的页面地址，如果服务器发现对邮箱页面访问的 referer 地址不是首页的地址，就可以断定是盗链。
```
 option: -e 可以设定 referer
 curl -e "http://www.live.com" http://mail.live.com
```

#### 下载重命名

文件下载后，文件名会增加指定的字符
```
 curl -o #1_#2.jpg http://www.bing.com/{aa,bb}/abc[1-5].jpg
```

#### 内部变量

可以用下列命令来获取网站的连接、传输以及总耗时：
```
 curl -o /dev/null -s -w '%{time_connect} %{time_starttransfer} %{time_total}' "https://mwbbs.eu.org/"
 -w 选项允许用户自定义输出格式，如：-w '%{url_effective}\t%{http_code}\t%{time_total}\n'
```

curl 还提供了许多其他变量，可以用来获取更详细的 HTTP 请求信息。这些变量通常以 %{variable_name} 的形式表示。

| Type | CMD | Explain |
|:---|:---|:---|
| + |
| rowspan="7" | 时间 | time_connect | 建立 TCP 连接所花费的时间，包括 DNS 解析、TCP 三次握手等 |
| time_starttransfer | 从请求开始到服务器开始传输响应数据的第一个字节所花费的时间 |
| time_total | 整个请求过程的总耗时，包括连接建立、数据传输、接收等所有环节 |
| time_appconnect | 建立应用程序层连接的时间 |
| time_namelookup | DNS 解析时间 |
| time_pretransfer | 请求开始前的准备时间 |
| time_redirect | 重定向所花费的时间 |
| rowspan="4" | 请求和响应 | http_code | HTTP 状态码 |
| url_effective | 最终访问的 URL |
| content_length | 响应内容长度 |
| speed_download | 下载速度 |
| rowspan="4" | 其他 | remote_ip | 远程服务器的 IP 地址 |
| remote_port | 远程服务器的端口 |
| local_ip | 本地使用的 IP 地址 |
| local_port | 本地使用的端口 |
