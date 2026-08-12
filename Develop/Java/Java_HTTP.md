---
source_title: Java HTTP
categories:
- Develop
- Java
last_modified: '2024-08-08T09:01:35Z'
---
#### Server
 ```
import java.net.ServerSocket;
import java.net.Socket;
ServerSocket serverSocket = new ServerSocket(port0);
Socket socket = serverSocket.accept();

# String IP_C = socket.getInetAddress().getHostAddress();
BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
inputLine = in.readLine();
out.println("String");

# Http 返回结构
String httpResponse = "HTTP/1.1 200 OK\r\n" +
  String.format("Content-Length: %s\r\n", httpResponseMessage.length()) +
  "Content-Type: text/plain\r\n" +
  "\r\n" +
  httpResponseMessage;
out.print(httpResponse);
out.flush();
```

#### Client
 ```
import java.net.Socket;
Socket socket1 = new Socket(host1, port1);
BufferedReader in1 = new BufferedReader(new InputStreamReader(socket1.getInputStream()));
PrintWriter out1 = new PrintWriter(socket1.getOutputStream(), true);
socket1.setSoTimeout(timeout);
out.println("String");
response = in.readLine();
```
