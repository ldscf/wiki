---
source_title: Tmux
categories:
- Develop
- Linux
last_modified: '2026-06-03T07:01:56Z'
---
在通过 SSH 远程连接服务器运行程序（如 Claude CLI 或长期脚本）时，网络不稳定常导致连接断开、前台进程被迫中止。Tmux 通过在服务器上维持一个独立的后台服务，可实现「断线留存、重连恢复」。

### 安装

| 操作系统 | 安装命令 |
|:---|:---|
| **macOS** | brew install tmux |
| **Ubuntu / Debian** | sudo apt install tmux |
| **CentOS / RHEL / Rocky** | sudo dnf install tmux |
> **验证安装**：tmux -V

修复：终端中触摸板上下滑动 -> 缓冲区滚动
```
 # ~/.tmux.conf
 set -g mouse on
```

### 使用

#### 新建

通过 SSH 连接服务器后，运行以下命令创建一个名为 claude 的后台空间：

: tmux new -s claude
* *注意：此时终端底部会出现一条**绿色状态栏**，代表已进入 Tmux 保护圈。在 Tmux 会话内，像平时一样启动任务。*

#### 断开
* **遭遇突发断网**：Tmux 会自动将窗口托管至后台。
* **主动退出登录**：依次按下组合键 Ctrl + b，松开后按 d (Detach)。

#### 恢复

返回之前的程序画面：

: tmux a -t claude

### 其它
* **关闭当前会话**：exit
* **查看当前所有后台会话**：tmux ls
* **关闭指定名字的会话**：tmux kill-session -t claude

> **小贴士**：Tmux 所有的快捷键都需要先按 **前缀键** Ctrl + b 唤醒。例如：分离会话是 Ctrl + b ➡️ d；如果未来需要分屏，左右分屏是 Ctrl + b ➡️ %。
