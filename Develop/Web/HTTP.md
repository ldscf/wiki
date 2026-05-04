---
source_title: HTTP
categories:
- Develop
- Web
last_modified: '2024-08-08T03:09:44Z'
---
超文本传输协议（HTTP）是一个用于传输超媒体文档（例如 HTML）的应用层协议。它是为 Web 浏览器与 Web 服务器之间的通信而设计的，但也可以用于其他目的。

HTTP 遵循经典的客户端—服务端模型，客户端打开一个连接以发出请求，然后等待直到收到服务器端响应。HTTP 是无状态协议，这意味着服务器不会在两个请求之间保留任何数据（状态）。

### HTTP header

HTTP 标头（HTTP header）允许客户端和服务器通过 HTTP 请求（request）或者响应（response）传递附加信息。一个 HTTP 标头由它的名称（不区分大小写）后跟随一个冒号（:），冒号后跟随它具体的值。该值之前的空格会被忽略。

#### 请求标头

包含有关要获取的资源或客户端或请求资源的客户端的更多信息。
- HTTP 方法请求
  - GET 方法请求指定资源的表示。使用 GET 的请求应该只用于请求数据，而不应该包含数据。
  - POST 方法发送数据给服务器。请求主体的类型由 Content-Type 标头指定。
  - PUT 请求方法创建一个新的资源或用请求的有效载荷替换目标资源的表示。  

PUT 和 POST 方法的区别是，PUT 方法是幂等的：调用一次与连续调用多次效果是相同的（即没有副作用），而连续调用多次相同的 POST 方法可能会有副作用，比如多次提交同一订单。
  - DELETE 请求方法用于删除指定的资源。
- 连接管理（Connection management）
  - Connection: 控制当前事务完成后网络连接是否保持打开状态。
  - * Keep-Alive: 控制持久连接应保持打开状态的时间。
- User-Agent: 用户代理字段是一个特征字符串，使得服务器和对等网络能够识别发出请求的用户代理的应用程序、操作系统、供应商或版本信息。  
  - 警告：使用用户代理字段进行浏览器检测以便为不同的浏览器提供不同的页面或者服务通常不是一个好主意。
- 内容协商（Content negotiation）
  - Accept: 通知服务器可以发回的数据类型。
  - Accept-Encoding: 编码算法，通常是压缩算法，用于返回的资源。
  - Accept-Language: 通知服务器有关服务器预期返回的人类语言。这是一个提示，不一定在用户的完全控制之下：服务器应该始终注意不要覆盖明确的用户选择（比如从下拉列表中选择一种语言）

#### 响应标头

响应标头包含有关响应的额外信息，例如响应的位置或者提供响应的服务器。是与响应消息主体无关的 HTTP 标头，可以用于 HTTP 响应。像 Age、Location 或 Server 都属于响应标头，被用于提供更详细的响应上下文。

#### 表示标头

表示标头包含资源主体的信息，例如主体的 MIME 类型或者应用的编码/压缩方案。
- Content-Type: 指示资源的媒体类型。
- Content-Encoding: 用于指定压缩算法。
- Content-Language: 描述面向受众的人类语言，以便用户可以根据自己的首选语言进行区分。
- Content-Location: 指示返回数据的备用位置

#### 有效负荷标头

有效负荷标头包含有关有效载荷数据表示的单独信息，包括内容长度和用于传输的编码。
- Content-Length: 资源的大小，以十进制字节数表示。

#### Sample

##### Get
 ```
GET /api/tags HTTP/1.1
Host: 192.168.0.10:11434
Connection: keep-alive
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) xyz.chatboxapp.app/1.3.17 Chrome/108.0.5359.215 Electron/22.3.27 Safari/537.36
Accept: */*
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN
```

##### Post
 ```
POST /api/generate HTTP/1.1
Host: 192.168.0.10:11434
User-Agent: User-Agent: curl/8.7.1
Accept: */*
Content-Length: 9
Content-Type: application/x-www-form-urlencoded
{a:1,b:2}
```

## 参考
1. [mozilla.org: HTTP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)
