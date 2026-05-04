---
source_title: Wssh
categories:
- Develop
- Linux
- Platform
last_modified: '2025-08-28T14:23:00Z'
---
WebSSH，用作 ssh 客户端来连接到 SSH 服务器。由 huashengdun 在 Github 上开源，项目地址为 https://github.com/huashengdun/webssh, 用 Python 编写，基于tornado、paramiko和xterm.js。

### Requirements
- Python 3.8+

### Quickstart
1. Install: pip3 install webssh
1. Upgrade: python3 -m pip install --upgrade webssh

### Server options
```
 # start a http server with specified listen port
 wssh --port=30080 --fbidhttp=False --log-file-prefix=log/l_webssh.log
```
 
```
 # start a https server, certfile and keyfile must be passed
 wssh --certfile='/path/to/cert.crt' --keyfile='/path/to/cert.key'
```
