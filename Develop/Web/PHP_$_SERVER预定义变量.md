---
source_title: PHP $ SERVER预定义变量
categories:
- Develop
- Php
- Web
last_modified: '2023-01-05T01:48:22Z'
---
  - PHP $ SERVER预定义变量详解**

$_SERVER 是 PHP 预定义变量之一，可以直接使用，它是一个包含了诸如头信息（header）、路径（path）及脚本位置（script locations）信息的数组。数组中的元素由 Web 服务器创建，但不能保证每个 Web 服务器都提供全部元素。

| Element/Code | 描述 |
|:---|:---|
| PHP_SELF | 返回当前执行脚本的文件名。 |
| GATEWAY_INTERFACE | 返回服务器使用的 CGI 规范的版本。 |
| SERVER_ADDR | 返回当前运行脚本所在的服务器的 IP 地址。 |
| SERVER_NAME | 返回当前运行脚本所在的服务器的主机名（比如 www.w3schools.cn）。 |
| SERVER_SOFTWARE | 返回服务器标识字符串（比如 Apache/2.2.24）。 |
| SERVER_PROTOCOL | 返回请求页面时通信协议的名称和版本（例如，"HTTP/1.0"）。 |
| REQUEST_METHOD | 返回访问页面使用的请求方法（例如 POST）。 |
| REQUEST_TIME | 返回请求开始时的时间戳（例如 1377687496）。 |
| QUERY_STRING | 返回查询字符串，如果是通过查询字符串访问此页面。 |
| HTTP_ACCEPT | 返回来自当前请求的请求头。 |
| HTTP_ACCEPT_CHARSET | 返回来自当前请求的 Accept_Charset 头（ 例如 utf-8,ISO-8859-1） |
| HTTP_HOST | 返回来自当前请求的 Host 头。 |
| HTTP_REFERER | 返回当前页面的完整 URL（不可靠，因为不是所有用户代理都支持）。 |
| HTTPS | 是否通过安全 HTTP 协议查询脚本。 |
| REMOTE_ADDR | 返回浏览当前页面的用户的 IP 地址。 |
| REMOTE_HOST | 返回浏览当前页面的用户的主机名。 |
| REMOTE_PORT | 返回用户机器上连接到 Web 服务器所使用的端口号。 |
| SCRIPT_FILENAME | 返回当前执行脚本的绝对路径。 |
| SERVER_ADMIN | 该值指明了 Apache 服务器配置文件中的 SERVER_ADMIN 参数。 |
| SERVER_PORT | Web 服务器使用的端口。默认值为 "80"。 |
| SERVER_SIGNATURE | 返回服务器版本和虚拟主机名。 |
| PATH_TRANSLATED | 当前脚本所在文件系统（非文档根目录）的基本路径。 |
| SCRIPT_NAME | 返回当前脚本的路径。 |
| SCRIPT_URI | 返回当前页面的 URI。 |

### PHP
```
 <?php
```

    print_r($_SERVER);
```
 ?>
```
