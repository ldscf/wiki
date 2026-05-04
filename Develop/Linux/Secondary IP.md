---
source_title: Secondary IP
categories:
- Develop
- Linux
last_modified: '2024-08-15T06:43:51Z'
---
辅助 IP (Secondary IP)

### 概念

辅助 IP 是绑定到一个物理网卡上的多个 IP 地址之一。除了主 IP 地址外，一个网卡还可以配置多个辅助 IP 地址。

### 用途
- 多主机名: 为一台服务器配置多个主机名，通过不同的 IP 地址访问不同的服务
- IP 地址管理: 将不同的服务绑定到不同的 IP 地址上，方便管理和配置

### 配置

网卡名称 eth0
- ifconfig eth0:1 192.168.0.251 netmask 255.255.255.0 up  

 up -> down for delete
- ip addr add 192.168.0.251/24 dev eth0  

 add -> del for delete  

ifconfig 不可见

### 说明
 ```

# Test, Ubuntu 20.04, Centos7
java SocketServer 10010
ip addr add 192.168.0.251/24 dev ens192
ip addr del 192.168.0.251/24 dev ens192
```

多台相同辅助 IP 主机，开相同端口，优先连接时以上次连接主机。最后 add 的主机并未优先，物理 IP 值较大主机可能优先。
