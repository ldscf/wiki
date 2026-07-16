---
source_title: LaunchAgent
categories:
- Apple
- Develop
- Mac
last_modified: '2026-06-29T07:57:23Z'
---
launchd 是 macOS 核心的初始化及进程管理框架（类似于 Linux 的 systemd），统一取代了传统的 rc.d、cron、init 和 xinetd。在 launchd 体系中，服务通过 Property List (.plist) 配置文件进行定义。

### 概述

launchd 将常驻任务主要划分为 LaunchAgent（用户级）和 LaunchDaemon（系统级）：

| 类型 | 配置文件存放路径 | 作用域与生命周期 | 运行权限 | 适用场景 |
|:---|:---|:---|:---|:---|
| LaunchAgent (用户专属) | ~/Library/LaunchAgents/ | 仅在当前用户登录会话期间加载与运行 | 当前登录用户权限 | 用户态脚本、SSH 隧道、桌面辅助进程 |
| LaunchAgent (全局用户) | /Library/LaunchAgents/ | 任意用户登录时，在其各自的用户会话中加载 | 当前登录用户权限 | 动态加载的全局环境配置或用户辅助工具 |
| LaunchDaemon (系统全局) | /Library/LaunchDaemons/ | 系统开机时加载，与用户是否登录无关 | root 或指定系统用户 | 数据库、Web 服务器、全局网络代理等底层服务 |

### Dynamic SSH Tunneling

在处理诸如 SSH 动态端口转发（SOCKS5 隧道）等需要常驻的进程时，传统的做法是通过 cron 或自定义 Shell 脚本每隔 60 秒轮询（如调用 lsof -i :1080）来检查端口并拉起服务。这种方式存在感知延迟高、频繁唤醒 CPU 导致资源浪费等问题。

更具工程严谨性的设计是利用 launchd 自身的守护机制：
1. 通过 KeepAlive: true 让 launchd 捕获进程退出信号，实现秒级自动拉起。
1. 将 SSH 连接逻辑整合到用户本地 SSH 客户端配置中，实现参数与服务管理层的低复杂度解耦。

#### 基础设施层

为了规避在 .plist 中硬编码长参数，在 ~/.ssh/config 配置：
 ```

# mc1.en 1080
Host proxy-mc1-en
    HostName mc1.en
    User bi
    DynamicForward 1080
    Compression yes
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

#### 服务配置层

在 ~/Library/LaunchAgents/local.ssh.proxy.plist 中定义服务。利用 launchd 原生接管标准流：
 ```

# mc1.en 1080
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDsPropertyList-1.0.dtd">
    Label
    local.ssh.proxy
    ProgramArguments
    
        /usr/bin/ssh
        -N
        proxy-mc1-en
    
    RunAtLoad
    
    KeepAlive
    
    StandardOutPath
    /Users/ldscfe/Library/Logs/ssh-proxy.log
    StandardErrorPath
    /Users/ldscfe/Library/Logs/ssh-proxy.err
```

### 命令

macOS 使用 launchctl 工具与 launchd 进行交互。
 ```

# PL_PATH="$HOME/Library/LaunchAgents/local.ssh.proxy.plist"

# 1. 激活并启动服务（设置开机自启并立刻运行）
launchctl bootstrap gui/$(id -u) "$PL_PATH"

# 2. 停止服务并解除自启
launchctl bootout gui/$(id -u) "$PL_PATH"

# 3. 检查服务运行状态与 PID
launchctl list | grep local.ssh.proxy
launchctl print gui/$(id -u)/local.ssh.proxy   # 完整运行状态
```

*注：launchctl list 返回结果的第一个字段如果为数字，代表当前运行的进程 PID；如果显示 - 且无报错，通常代表服务已成功运行完毕并正常退出（如非持续性定时任务）。若第二列显示非零数字，则代表上一次退出的非正常错误码（Exit Code）。*
