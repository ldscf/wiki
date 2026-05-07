---
source_title: Proxy Server
categories:
- Develop
- Linux
- Windows
last_modified: '2026-04-07T15:07:03Z'
---
设置代理服务器，使内网客户端可以通过代理服务器的 Wireguard 上网。

### Windows11

环境：两个网卡，其中一个 WiFi 可上网（192.168.0.100），另一个有线（192.168.137.1），Wireguard（10.0.0.10）。

配置：192.168.137 网段上的主机，可通过 10.0.0.10 上网。

#### 啟用 IP 轉發功能
```
 reg add HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters /v IPEnableRouter /t REG_DWORD /d 1 /f
 # 重启
```

#### 設置 VPN 網絡共享
```
 VPN 網卡（10.0.0.10），右鍵屬性 --> 共享，選擇本地網卡（192.168.137.1）。
 確保本地網卡（192.168.137.1）使用靜態 IP
 192.168.137.1
 255.255.255.0
 DNS: 8.8.8.8
```

#### 檢查路由表
```
 確保有一條路由，將流量發送到 VPN 的網卡
 route print
```

   Network Destination    Netmask        Gateway          Interface    Metric

   0.0.0.0                0.0.0.0        10.0.0.10        [VPN Interface]

#### 設置 137 網段的主機
```
 192.168.137.101
 255.255.255.0
 網關：192.168.137.1
 DNS: 8.8.8.8
```

### Ubuntu 20.04

环境：一个网卡（ens160 - 192.168.0.100），Wireguard（u31 - 10.0.0.10）

配置：192.168.0 网段上的主机，可通过 10.0.0.10 上网

#### 啟用 IP 轉發功能
```
 # /etc/sysctl.conf
 # sysctl -p
 net.ipv4.ip_forward = 1
```

#### 修改网卡名称

如果有多台服务器的话，可能要统一网卡名称。
 ```
#确认网卡 MAC 地址
ip link show
  -> "00:0c:29:79:95:c1"

# /etc/netplan/99-custom-interfaces.yaml
network:
    version: 2
    ethernets:
        ens3:       # 这里的 ID 可以随便写，通常写成目标名称
            match:
                macaddress: "00:0c:29:79:95:c1"
            set-name: ens3
            dhcp4: true
netplan apply   # 或者重启
```

#### 設置 NAT
```
 # 設置 NAT，讓來自 192.168.0 網段的流量通過 u31（10.0.0.10）轉發
 iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o u31 -j MASQUERADE
```

   -t nat：使用 NAT 表

   -A POSTROUTING：在 POSTROUTING 鏈中添加規則

   -s 192.168.0.0/24：源地址是 192.168.0 網段

   -o u31：指定 VPN 網卡（通过 wg-quick up u31 产生，u31 是 /etc/wireguard/u31.conf，可以通過 ip a 检查確認）

   -j MASQUERADE：啟用地址偽裝
```
 # 删除：iptables -t nat -D POSTROUTING -s 192.168.0.0/24 -o u31 -j MASQUERADE
 # 清空：iptables -t nat -F POSTROUTING
 # 更通用的写法（不限制源 IP 网段）：**iptables -t nat -A POSTROUTING -o u31 -j MASQUERADE**
```

#### 配置內部網段流量轉發
```
 iptables -A FORWARD -i ens160 -o u31 -j ACCEPT
 iptables -A FORWARD -i u31 -o ens160 -j ACCEPT
 # 删除：
 iptables -D FORWARD -i ens160 -o u31 -j ACCEPT
 iptables -D  FORWARD -i u31 -o ens160 -j ACCEPT
 # 清空：iptables -F FORWARD
```

#### 保存防火牆規則
```
 # Ubuntu 中，iptables 設置默認不會持久化
 apt install iptables-persistent
 netfilter-persistent save
 netfilter-persistent reload
```

#### 設置 192.168.0 網段的主機
```
 192.168.0.101
 255.255.255.0
 網關：192.168.0.100
 DNS: 8.8.8.8
```

#### MTU

如果出现访问网站、视频（如 Youtube）正常，但有些游戏 load 中间卡住情况，可能是 MTU 问题。

##### MTU 排查
```
 # ping 一个远程主机，使用 -M do 禁用分片，找到不分片的最大值：
 ping -c 3 -M do -s 1420 8.8.8.8
 -> ping: local error: message too long, mtu=1420 -> 缩小 -s 的值，直到：
 ping -c 3 -M do -s 1392 8.8.8.8
 -> 1400 bytes from 8.8.8.8: icmp_seq=1 ttl=119 time=166 ms
 # 此时：
 路径 MTU = 1392
 PMTU = 1392 + 28(IP 头 20 字节 + ICMP 头 8 字节) = 1420
```

##### 调整各路径 MTU
1. 调整 WireGuard 的 MTU（u31.conf）
1. 调整 u31    : ip link set dev u31 mtu 1420（这个貌似没用）
1. 调整 ens160 : ip link set dev ens160 mtu 1420（这个貌似没用）

说上面两个没用的，尤其是 u31 这个，如果两个同时改为 1422，ping -c 3 -M do -s 1393 8.8.8.8 是可以正常的，但游戏又会卡住了。因为跟下面的 MSS 钳制有关。

##### 调整 iptables (MSS 钳制)
```
 iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -o u31 -j TCPMSS --clamp-mss-to-pmtu
```

这条规则将通过 u31 接口的 TCP SYN 数据包的 MSS 值钳制到 PMTU，这通常是解决 MTU 问题的最有效方法。

同样，ens160 接口也需要
```
 iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -o ens160 -j TCPMSS --clamp-mss-to-pmtu
```

更加通用的写法：不指定网卡，针对所有经过服务器转发的 SYN 包进行钳制
```
 iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
```

或者使用极高稳定性的强制模式（MSS 设为 1300），解决“灵异”卡顿（代价是传输大数据（如 4K 视频或大文件下载）时，包的数量会增多，开销随之增加）
```
 iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1300
```

MSS 钳制只能管 TCP 包，而一个合理的 MTU（如 1380-1420）可以确保 UDP 流量、ICMP 探测以及协议本身的封装不至于在底层物理链路上“撞墙”。

##### IPv4

如果 WireGuard 主机没有配置 IPv6 转发，最简单的办法是让所有流量都强制走 IPv4。
```
 # 客户端配置文件（u31.conf），将 AllowedIPs 中的 IPv6 部分去掉：
 AllowedIPs = 0.0.0.0/0, ::/0  ->  AllowedIPs = 0.0.0.0/0
```

或者调整系统 IPv4/IPv6 优先级。
```
 # /etc/gai.conf
 precedence ::ffff:0:0/96  100    # 默认注释
```
