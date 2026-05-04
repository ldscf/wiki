---
source_title: CGI
categories:
- Develop
- Web
last_modified: '2023-01-05T02:25:11Z'
---
CGI

CGI（The Common Gateway Interface）通用网关接口，定义web服务器和客户脚本进行信息交互的一系列标准。

CGI 应用程序能与浏览器进行交互，还可通过数据API与数据库服务器等外部数据源进行通信，从数据库服务器中获取数据。

CGI 是一个标准化的协议，CGI程序能够用 Python, PERL, Shell, C or C++等语言来实现。

### HTTP报文信息

| Header | Description |
|:---|:---|
| Content-type: | A MIME string defining the format of the file being returned. Example is Content-type:text/html |
| Expires: Date | The date the information becomes invalid. This should be used by the browser to decide when a page needs to be refreshed. A valid date string should be in the format 01 Jan 1998 12:00:00 GMT. |
| Location: URL | The URL that should be returned instead of the URL requested. You can use this filed to redirect a request to any file. |
| Last-modified: Date | The date of last modification of the resource. |
| Content-length: N | The length, in bytes, of the data being returned. The browser uses this value to report the estimated download time for a file. |
| Set-Cookie: String | Set the cookie passed through the *string* |

### CGI环境变量

| CGI环境变量名称 | 说明 |
| REQUEST_METHOD | 请求类型，如“GET”或“POST” |
| CONTENT_TYPE | 被发送数据的类型 |
| CONTENT_LENGTH | 客户端向标准输入设备发送的数据长度，单位为字节 |
| QUERY_STRING | 查询参数，如“id=10010&sn=liigo” |
| SCRIPT_NAME | CGI脚本程序名称 |
| PATH_INFO | CGI脚本程序附加路径 |
| PATH_TRANSLATED | PATH_INFO对应的绝对路径 |
| REMOTE_ADDR | 发送此次请求的主机IP |
| REMOTE_HOST | 发送此次请求的主机名 |
| REMOTE_USER | 已被验证合法的用户名 |
| REMOTE_IDENT | WEB服务器的登录用户名 |
| AUTH_TYPE | 验证类型 |
| GATEWAY_INTERFACE | 服务器遵守的CGI版本，如：CGI/1.1 |
| SERVER_NAME | 服务器主机名、域名或IP |
| SERVER_PORT | 服务器端口号 |
| SERVER_PROTOCOL | 服务器协议，如：HTTP/1.1 |
| DOCUMENT_ROOT | 文档根目录 |
| SERVER_SOFTWARE | 服务器软件的描述文本 |
| HTTP_ACCEPT | 客户端可以接收的MIME类型，以逗号分隔 |
| HTTP_USER_AGENT | 发送此次请求的web浏览器 |
| HTTP_REFERER | 调用此脚本程序的文档 |
| HTTP_COOKIE | 获取COOKIE键值对，多项之间以分号分隔，如：key1=value1;key2=value2 |

### Cookies信息
- Expires：包含Cookies的过期信息。如果变量值为空，当客户端关闭浏览器时，Cookies就会过期。
- Domain：web站点的域名信息。
- Path：设置Cookies的web页或目录的路径。如果想要从任何页面或目录获取Cookies信息，此变量设为空值。
- Secure：如果该字段设置为"secure"，那么Cookies将只能被安全服务器获取，如果该字段为空，则没有该限制。
- Name=Value：Cookies以键-值对的形式设置或获取。
