---
source_title: Linux 核心系统服务
categories:
- Develop
- Linux
last_modified: '2026-06-26T02:21:08Z'
---
规范和记录自定义系统常驻任务（如：网络隧道、代理、业务进程等）在 Systemd 架构下的托管流程。

### Systemd

| 标准命令 | 功能说明 |
|:---|:---|
| systemctl daemon-reload | **核心：**变更任意服务文件后，必须执行此命令刷新 Systemd 内部状态 |
| systemctl start [服务名] | 启动 |
| systemctl restart [服务名] | 重启 |
| systemctl stop [服务名] | 停止 |
| systemctl enable [服务名] | 开机自启动 |
| systemctl status [服务名] | 查看 |

### 配置模板

自定义服务文件统一存放于：/etc/systemd/system/ 

多数前台阻塞运行的常驻程序推荐使用

[Unit]

Description=【服务的简要描述和名称】

After=network-online.target

Wants=network-online.target

[Service]

Type=simple

User=【执行该服务的系统用户（如 root, bi等）】

WorkingDirectory=【程序运行的绝对路径根目录】

ExecStart=【启动命令：使用绝对路径可执行文件，后面跟运行参数】
1. 自愈与优化配置

# RestartSec=60 每次异常退出后，等待 60 秒再进行下一次重启尝试

Restart=always

RestartSec=10

StartLimitIntervalSec=0

LimitNOFILE=65535

[Install]

WantedBy=multi-user.target

**network.target 与 network-online.target**

在配置 [Unit] 块的网络依赖时，需要区分以下两种声明：

# After=network-online.target + Wants=network-online.target (网络隧道/代理类必选)： 代表系统网络已经彻底在线，且网卡已成功分配到 IP 地址。SSH 反向隧道、Socks5 动态代理等服务开机时需要立刻解析域名并连接远程服务器，必须使用此组合，否则会因为开机瞬间网络未就绪而导致连接直接闪退或频繁报错重启。

# After=network.target (本地监听类推荐)： 仅代表系统的网络管理服务（如 NetworkManager）进程已启动。此时网卡可能还没有拿到 IP 地址。适合用于在本地监听端口的服务（如 MySQL、自研 SRDS等），因为它们启动时不需要立刻访问外网。  

After=network.target 改成 After=network-online.target 是可以的，但可能会导致该服务启动变慢。

### 命令行与 Systemd

在终端手动执行命令时，往往会习惯性地加入一些“后台运行”或“依赖当前 Shell 环境”的参数。但一旦将这些命令托管给 Systemd，其行为逻辑会发生根本性转变。

以下是典型服务在「手动执行」与「Systemd 托管」时的差异对比及对应的完整配置文件：

ssh-tunnel-18082.service

[Unit]

Description=SSH Reverse Tunnel - Port 18082

After=network-online.target

Wants=network-online.target

[Service]

Type=simple

User=root

ExecStart=/usr/bin/ssh -N \

  -R 18082:localhost:18082 \

  -o ServerAliveInterval=30 \

  -o ServerAliveCountMax=3 \

  -o StrictHostKeyChecking=yes \

  -o ExitOnForwardFailure=yes \

  ldscf4@mc3.en

Restart=always

RestartSec=600

StartLimitIntervalSec=0

[Install]

WantedBy=multi-user.target

***命令行习惯：**手动执行时，-f 让 SSH 主动切入后台（Fork）释放当前终端，方便继续敲其他命令。  

***Systemd 陷阱：**在 Type=simple 模式下，Systemd 要求主进程必须卡在前台。如果带了 -f，SSH 启动后会立刻复制一个子进程然后结束主进程。Systemd 会误判定“该服务已正常退出/崩溃”，随后根据 Restart=always 机制每隔几秒就疯狂重启，导致隧道永远建立不起来。
  
ssh-proxy@.service  

*(注意文件名中的 @ 符号)*

[Unit]

Description=Dynamic SSH Tunneling Service on Port %I

After=network-online.target

Wants=network-online.target

[Service]

Type=simple

User=root

EnvironmentFile=/etc/default/ssh-proxy-%i

ExecStart=/usr/bin/ssh -N $SSH_ARGS

Restart=always

RestartSec=10

StartLimitIntervalSec=0

[Install]

WantedBy=multi-user.target

***便捷扩展快捷姿势：**  
1. 新建 /etc/default/ssh-proxy-1080 写入：SSH_ARGS="-C -D 1080 bi@mc1.en"  
2. 执行：systemctl enable --now ssh-proxy@1080.service 即可秒级生效。

除了 -f 导致的死循环问题外，Systemd 内部没有配置完整的 $PATH 环境变量，它找不到裸写的 ssh 命令。所有可执行文件必须写成绝对路径 /usr/bin/ssh。  
  
利用带有 @ 符号的模板服务，可以使高频新增相似隧道时免去重复编写 service 文件的繁琐步骤。
  
srds-server.service

[Unit]

Description=SRDS High-Performance Storage Server

After=network.target

[Service]

Type=simple

User=bi

Group=bi

WorkingDirectory=/u01/github/srds

ExecStart=/u01/github/srds/bin/srds-server --config /u01/github/srds/etc/srds.conf

LimitNOFILE=65535

Restart=always

RestartSec=5

[Install]

WantedBy=multi-user.target
- 末尾的 & 会使进程组后台化，同样会欺骗 Systemd 导致无休止重启。  
- 手动执行时，常用 ../ 等相对路径，但 Systemd 启动时的初始工作目录默认是系统根目录（/）。如果不使用 WorkingDirectory 显式指定，程序会因为找不到配置文件或数据库日志路径而直接闪退。

| 场景 | 💻 终端命令 | ⚙️ Systemd | 说明 |
|:---|:---|:---|:---|
| **SSH 反向隧道** | ssh -f -N -R 18082:localhost:18082 ldscf4@mc3.en | **禁止 -f 参数** |
| **动态 Socks5 代理** | ssh -f -C -N -D 1080 bi@mc1.en | **禁止** **-f，且需绝对路径** |
| **自研业务进程 (如 SRDS 等)** | ./srds-server --config ../etc/srds.conf & | **禁止末尾加 &，禁止相对路径** |
**为什么 Systemd 无法直接套用命令行环境？**

除了上述的参数冲突，Systemd 服务在独立沙盒中运行，默认**不会**继承登录终端时的任何用户配置：
1. **文件描述符限制 (Ulimit) 独立性：**  

在终端敲 ulimit -n 65535 或在 /etc/security/limits.conf 中修改的限制，对 Systemd 完全无效。高并发的自研业务进程或 AI 频繁拉取的进程，必须在 [Service] 块中强行指定 LimitNOFILE=65535，否则会直接报 Too many open files。
1. **无交互无人值守：**  

手动敲 SSH 连接时，如果遇到未知的服务器，终端会弹窗提示 Are you sure you want to continue connecting (yes/no)?。但在 Systemd 后台，没有任何交互界面。因此网络隧道类服务必须强制带上 -o StrictHostKeyChecking=no（或提前用 root 账号手动连接一次录入 known_hosts），否则服务会无限期死锁在后台等待一个永远不会出现的键盘输入。

### 注意

#**禁止后台派生 (-f / daemonize)**：Systemd 在 Type=simple 模式下，要求配置的命令必须阻塞在前台。如果程序自带类似 -f (SSH) 或 daemonize yes (Redis/自研服务) 的参数，必须**显式去掉或关闭**，否则 Systemd 会误判进程已挂起从而陷入无限重启。

#**用户与权限隔离**：网络类、涉及敏感端口绑定（< 1024）的通用基础设施可使用 User=root；而团队自研的业务、应用层代码，推荐单独创建低权限用户（如 User=bi）运行，并在 WorkingDirectory 中限定其作用域。

#**环境变量与软限制限制**：Systemd 服务启动时**不会**加载用户的 ~/.bashrc 或 /etc/security/limits.conf 配置文件。因此，程序若需要高并发连接（如大模型 Agent 循环、KV 数据库），必须在 [Service] 块中通过 LimitNOFILE=65535 显式指定文件描述符限制。
