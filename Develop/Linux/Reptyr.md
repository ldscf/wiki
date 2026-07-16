---
source_title: Reptyr
categories:
- Develop
- Linux
last_modified: '2026-06-26T02:54:47Z'
---
reptyr（Reparent Terminal Redirection）是一个 Linux 系统级运维工具。能够将一个正在运行的后台进程，强行从它原本绑定的终端（TTY/PTS）中剥离，并“跃迁”嫁接到当前用户所处的终端窗口中。

在没有提前使用 tmux / screen，或者终端管理器自身死锁卡死时，reptyr 是唯一的“进程后悔药”。

### 原理

在 Linux 架构中，进程的输入输出（stdin/stdout/stderr）在诞生时就与某个特定的伪终端文件（如 /dev/pts/2）紧密绑定。reptyr 能够打破这种绑定，其底层实现了内核级别的重定向操作：
```
 [旧终端 pts/2] (卡死)                               [当前新终端 pts/0]
```

       ^                                                   ^

       | (断开旧管道)                                        | (接管新管道)

       X                                                   |

  [claude 进程组] <============= [reptyr 注入 ptrace] ============+
1. Ptrace 附加：reptyr 利用 Linux 的 ptrace 系统调用，像调试器（GDB）一样强制挂载到目标进程上并使其暂停。
1. 代码注入与 FD 替换：在目标进程的内存空间中强行注入 dup2() 系统调用，关闭其原本的旧 TTY 文件描述符，将其重定向（Reparenting）到当前新终端的 pts。
1. 控制权移交：reptyr 退出调试状态，恢复进程运行。此时目标进程的生存宿主已彻底改变。

### 案例

抢救卡死的 Agent 进程。

#### 背景

由于系统 tmux server 发生死锁，AI 编码 Agent 被困在无法连接的会话中。通过 ps -ef 盘点出关键进程树：
- 父进程 (Python 壳)：PID 1470951 (claude) 运行在已失联的 pts/2
- 子进程 (AI 核心)：PID 1470955 (claude)

#### 前置

解除内核安全防御：Linux 内核为了防止恶意软件利用 ptrace 偷窥内存，默认限制了附加权限。即便用 root 身份，直接劫持也会报 Permission denied。

临时将内核防御降级：
```
 echo 0 > /proc/sys/kernel/yama/ptrace_scope
```

#### 劫持动作

###### 直接劫持

若执行 reptyr 1470955，系统会报错：
```
 [-] Process 1470951 shares 1470955's process group. Unable to attach.
```

原因：claude 与其父进程 claude 属于同一个复杂进程组。为了防止强行剥离导致父子管道破裂，reptyr 默认会拒绝。

###### 整体劫持

使用 -T（Steal Background Process Group）参数，强行把整个进程组（父进程 + 子进程）当作一个整体一并拖回当前终端：
```
 reptyr -T 1470955
```

#### 生命周期接管

当整个 Claude 进程组被拉到你的新终端（假设是 pts/0）后，需要进行以下收尾，使其能够重新安全地在后台常驻：
- 第一步：激活屏幕

刚拉过来时屏幕往往一片漆黑，连续敲击 Enter (回车) 或 Ctrl + L (清屏) 即可强制 Claude 重新绘制终端交互界面。
- 第二步：安全转入系统后台

绝对不能直接关窗口或 Ctrl + C，否则进程会连带死亡。请依次输入：
```
 Ctrl + Z         # 暂停当前进程组并丢入后台
 bg               # 让该后台进程组恢复运行
 disown -h %1     # 切断该进程与当前临时终端的生死绑定
```

### 注意

| 限制维度 | 表现与规则 | 解决方案 |
|:---|:---|:---|
| 权限限制 | 必须拥有和目标进程相同的 UID。root 用户拥有最高特权，可跨用户劫持。 | 使用 root 账号或 sudo reptyr。 |
| 多子进程限制 | 目标进程如果拥有复杂的守护进程树或协同管道，直接劫持会失败。 | 必须附加 -T 参数以劫持整个进程组。 |
| 图形界面限制 | reptyr 仅支持纯文本 TTY/PTS 的进程重定向。 | 无法用于劫持 X11 或 Wayland 图形界面程序。 |
