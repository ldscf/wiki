---
source_title: SSH 反向隧道
categories:
- Develop
- Linux
last_modified: '2026-06-26T02:57:12Z'
---
有在不同内网的机器 C 和 L，均无公网 IP，此时 C 无法向 L 发起连接。

现有一公网 IP 机器 P，C 与 L 均可主动向 P 发起连接。此时可透过 P，可完成 C 向 L 发起连接。需要：
1. Env：L 可用证书连接到 P
1. Env：P 的 sshd_config 配置打开转发功能：GatewayPorts yes（重启 sshd）
1. Env：P 的 firewall 打开相应端口
1. L 向 P 主动建立一个 SSH 反向隧道，如：将 P 的 10022 端口转发到 L 的 22 端口
```
 # 在 L 上执行，在 P 上建立反向隧道监听端口 10022
 # 超时会断掉，加参数无用：-o TCPKeepAlive=yes
 ssh -T -f -N -g -R :10022:127.0.0.1:22 xxx1@P
```

参数说明：
* **-T** 不分配伪终端
* **-f** 使 ssh 进程在用户输入密码之后转入后台运行
* **-N** 不执行远程指令，即代理服务器不需执行指令，只作端口转发
* **-g** 允许代理服务器连接到本地转发端口
* **-R** 将代理服务器指定端口上的连接转发到本机端口
* **:10022:127.0.0.1:22**表示本机回环接口的 22 端口连接到远程主机的 10022 接口，因远程主机 10022 绑定的地址为空，所以远程主机会监听其所有网络接口的 10022 端口

### 代理跳板参数

现代 SSH 客户端支持直接在命令行中指定跳板路径（在内网机器 C 上执行下面命令，可连接到 L）：
```
 ssh -i ~/.ssh/id_rsa_xxx -J xxx1@P xxx2@localhost -p 10022
```

### 本地端口转发

在内网机器 C 上使用 -L 本地端口转发：
```
 ssh -i ~/.ssh/id_rsa_rds -N -L 10022:localhost:10022 xxx1@P
 ssh xxx2@localhost -p 10022
```

### [Linux_核心系统服务](#Linux_核心系统服务)
1. /etc/systemd/system/ssh-tunnel-18082.service        # 路径
1. systemctl daemon-reload                             # 修改或新建服务文件后，通知 systemd 重新加载配置
1. systemctl restart ssh-tunnel-18082.service          # 立即重启（或启动）该 SSH 隧道服务
1. systemctl enable ssh-tunnel-18082.service           # 将该服务设置为开机自启动
 ```
[Unit]
Description=SSH Reverse Tunnel                        # 服务的简要描述：SSH 反向隧道
After=network-online.target                           # 等待网络完全在线后再启动该服务
Wants=network-online.target                           # 弱依赖网络在线服务，配合 After 确保网络可用
[Service]
Type=simple                                           # 服务类型为 simple，即运行 ExecStart 中的命令为主进程
User=root                                             # 以 root 用户身份运行此服务（请确保 root 的 known_hosts 已配置）
ExecStart=/usr/bin/ssh -N \                           # -N 只建立隧道而不执行远程命令
  -R 18082:localhost:18082 \                          # 反向隧道配置：将远程主机的 18082 端口转发到本地的 18082 端口
  -o ServerAliveInterval=30 \                         # 客户端每隔 30 秒向服务器发送一次心跳信号
  -o ServerAliveCountMax=3 \                          # 如果连续 3 次（共 90 秒）没收到服务器响应，则断开连接以触发重启
  -o StrictHostKeyChecking=yes \                      # 严格进行主机密钥检查，防止中间人攻击（需提前在 known_hosts 录入密钥）
  -o ExitOnForwardFailure=yes \                       # 如果远程端口（18082）绑定失败，SSH 立即报错退出，从而让 systemd 知晓并重启
  ldscf4@mc3.en                                       # 连接的目标远程服务器用户名及 IP 地址
Restart=always                                        # 无论因何种原因退出（正常退出或报错），systemd 都会自动重启该服务
RestartSec=600                                        # 每次异常退出后，等待 600 秒再进行下一次重启尝试
StartLimitIntervalSec=0                               # 禁用 systemd 的启动频率限制，即使无限次失败也会永久尝试重启
[Install]
WantedBy=multi-user.target                            # 当系统进入多用户模式（正常开机启动状态）时，随系统一起自动启动
```
