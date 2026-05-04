---
source_title: Telnet
categories:
- Develop
- Linux
last_modified: '2025-06-06T01:04:23Z'
---
Telnet 协议是 TCP/IP 协议族中的一员，是 Internet 远程登录服务的标准协议和主要方式，提供了在本地计算机上完成远程主机工作的能力。

执行 telnet 指令开启终端机阶段作业，并登入远端主机。

### 交互
```
 telnet $IP $PORT
```

### 批量
```
 { echo $CMD1; echo $CMD2; } | telent $IP $PORT
```

### 退出
```
 ^]
 quit
```
