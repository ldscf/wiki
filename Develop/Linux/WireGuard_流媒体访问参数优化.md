---
source_title: WireGuard 流媒体访问参数优化
categories:
- Develop
- Linux
- Platform
last_modified: '2026-07-26T14:09:04Z'
---
本文档提供一套针对视频流媒体场景的 WireGuard 参数优化方案。

### 适用场景
- 视频播放偶尔卡顿、缓冲
- VPN 出口带宽充足但实际播放体验不佳

### 优化方案

#### 1. TCP 缓冲区优化

高延迟链路需要更大的 TCP 缓冲区来填满带宽延迟管道（BDP）。计算公式：

BDP = 带宽(Mbps) × RTT(ms) / 8

例如：25 Mbps × 228 ms / 8 ≈ 712 KB

| 参数 | 默认值 | 建议值 | 说明 |
|:---|:---|:---|:---|
| +TCP 缓冲区参数 |
| net.core.rmem_max | 212992 (208KB) | 16777216 (16MB) | TCP 接收缓冲区上限 |
| net.core.wmem_max | 212992 (208KB) | 16777216 (16MB) | TCP 发送缓冲区上限 |
| net.ipv4.tcp_rmem | 4096 131072 6291456 | 4096 131072 16777216 | TCP 接收缓冲区（最小 默认 最大） |
| net.ipv4.tcp_wmem | 4096 16384 4194304 | 4096 65536 16777216 | TCP 发送缓冲区（最小 默认 最大） |
| net.ipv4.tcp_window_scaling | 1 | 1 | TCP 窗口缩放（保持开启） |
| net.ipv4.tcp_timestamps | 1 | 1 | TCP 时间戳（保持开启） |
| net.ipv4.tcp_moderate_rcvbuf | 1 | 1 | 自动调节接收缓冲区（保持开启） |
配置命令：
```
 sysctl -w net.core.rmem_max=16777216
 sysctl -w net.core.wmem_max=16777216
 sysctl -w "net.ipv4.tcp_rmem=4096 131072 16777216"
 sysctl -w "net.ipv4.tcp_wmem=4096 65536 16777216"
```

持久化配置，写入 /etc/sysctl.d/99-wireguard.conf：
```
 net.core.rmem_max = 16777216
 net.core.wmem_max = 16777216
 net.ipv4.tcp_rmem = 4096 131072 16777216
 net.ipv4.tcp_wmem = 4096 65536 16777216
 net.ipv4.tcp_window_scaling = 1
 net.ipv4.tcp_timestamps = 1
 net.ipv4.tcp_moderate_rcvbuf = 1
```

#### 2. 拥塞控制算法切换

高延迟链路上，BBR 相比 Cubic 能更有效地利用带宽，减少缓冲膨胀（bufferbloat）。

| 参数 | 默认值 | 建议值 | 说明 |
|:---|:---|:---|:---|
| +拥塞控制参数 |
| net.ipv4.tcp_congestion_control | cubic | bbr | 拥塞控制算法 |
| net.core.default_qdisc | fq_codel | fq | 队列调度（BBR 搭配 fq 更优） |
配置命令：
```
 sysctl -w net.ipv4.tcp_congestion_control=bbr
 sysctl -w net.core.default_qdisc=fq
```

追加到 /etc/sysctl.d/99-wireguard.conf：
```
 net.ipv4.tcp_congestion_control = bbr
 net.core.default_qdisc = fq
```

#### 3. WireGuard MTU 调整

WireGuard 默认 MTU 为 1420。MTU 过低会降低有效载荷比例，增加协议开销。

| 参数 | 建议值 | 说明 |
|:---|:---|:---|
| +MTU 参数建议 |
| MTU | 1420 | WireGuard 接口 MTU |
配置方法：

###### 运行时生效
```
 ip link set <接口名> mtu 1420
```

###### 持久化配置

编辑 /etc/wireguard/<接口名>.conf，在 [Interface] 段添加：
```
 [Interface]
 MTU = 1420
```

#### 4. MSS Clamping 调整

MSS（最大段大小）限制了 TCP 段的有效载荷。在 MTU=1420 时，自然 MSS 为 1380（1420 - 40 字节 IP/TCP 头）。复杂网络可以配置成 1340，过度降低 MSS 会浪费带宽。

| 参数 | 建议值 | 说明 |
|:---|:---|:---|
| +MSS 参数建议 |
| TCPMSS | 1340 | iptables mangle 表 FORWARD 链 |
配置命令（运行时生效，重启失效）：
```
 iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1340
```

持久化方法取决于系统：

***iptables-persistent**：netfilter-persistent save

***firewalld**：firewall-cmd --runtime-to-permanent

***rc.local**：/etc/rc.local 中添加 iptables 命令

### 一键配置脚本

将以下脚本保存为 wireguard_optimize.sh，以 root 权限执行：
 ```
#!/bin/bash
set -e
echo "==== WireGuard 流媒体访问参数优化 ===="
#1. TCP 缓冲区
sysctl -w net.core.rmem_max=16777216
sysctl -w net.core.wmem_max=16777216
sysctl -w "net.ipv4.tcp_rmem=4096 131072 16777216"
sysctl -w "net.ipv4.tcp_wmem=4096 65536 16777216"
sysctl -w net.ipv4.tcp_window_scaling=1
sysctl -w net.ipv4.tcp_timestamps=1
sysctl -w net.ipv4.tcp_moderate_rcvbuf=1
#2. BBR 拥塞控制
sysctl -w net.ipv4.tcp_congestion_control=bbr
sysctl -w net.core.default_qdisc=fq
#3. 持久化
cat > /etc/sysctl.d/99-wireguard.conf << 'EOF'
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.ipv4.tcp_rmem = 4096 131072 16777216
net.ipv4.tcp_wmem = 4096 65536 16777216
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_timestamps = 1
net.ipv4.tcp_moderate_rcvbuf = 1
net.ipv4.tcp_congestion_control = bbr
net.core.default_qdisc = fq
EOF
#4. WireGuard MTU（修改接口名）
WG_IF="u31"
ip link set "$WG_IF" mtu 1420 2>/dev/null && echo "MTU 已调整为 1420" || echo "MTU 调整失败"
sed -i "s/^MTU.*/MTU = 1420/" /etc/wireguard/"$WG_IF".conf 2>/dev/null || true
#5. MSS Clamping
iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1340
echo "==== 优化完成 ===="
```

### 验证命令

优化完成后，使用以下命令验证配置：
```
 # TCP 缓冲区
 sysctl net.core.rmem_max net.core.wmem_max
```
```
 # 拥塞控制
 sysctl net.ipv4.tcp_congestion_control net.core.default_qdisc
```
```
 # MTU
 ip link show <接口名> | grep mtu
```
```
 # MSS
 iptables -t mangle -L FORWARD -v -n | grep TCPMSS
```
```
 # WireGuard 状态
 wg show <接口名>
```
```
 # TCP 连接详情（观察 rcv_space 和拥塞控制算法）
 ss -tin
```

### 参数对照表

| 参数 | 优化前 | 优化后 |
|:---|:---|:---|
| +优化前后参数对照 |
| net.core.rmem_max | 212992 | 16777216 |
| net.core.wmem_max | 212992 | 16777216 |
| net.ipv4.tcp_rmem | 4096 131072 6291456 | 4096 131072 16777216 |
| net.ipv4.tcp_wmem | 4096 16384 4194304 | 4096 65536 16777216 |
| 拥塞控制 | cubic | bbr |
| 队列调度 | fq_codel | fq |
| WireGuard MTU | 1420 |
| MSS Clamping | 1340 |

### 注意事项
- 以上配置适用于 WireGuard 视频流媒体优化场景
- TCP 缓冲区增大不会显著增加内存消耗，内核会按需分配
- BBR 需要 Linux 内核 >= 4.9
- MSS Clamping 规则位于 iptables mangle 表 FORWARD 链，重启后需重新添加
- WireGuard MTU 修改已同步到配置文件，重启后自动生效
