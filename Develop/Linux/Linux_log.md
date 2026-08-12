---
source_title: Linux log
categories:
- Develop
- Linux
last_modified: '2026-03-26T06:07:25Z'
---
Linux 日志文件

路径: /var/log

| 文件名称 | 内容 |
|:---|:---|
| + |
| messages | 整体系统信息、启动日志、mail/cron/daemon/kern/auth 等内容 |
| dmesg | 内核缓冲信息（kernel ring buffer，系统启动时，在屏幕上显示的硬件相关信息） |
| auth.log | 系统授权信息，包括用户登录和使用的权限机制等 |
| boot.log | 系统启动日志 |
| daemon.log | 系统后台守护进程日志信息 |
| dpkg.log | 安装或 dpkg 命令清除软件包的日志 |
| kern.log | 内核日志，有助于在定制内核时解决问题 |
| lastlog | 用户最近登录信息，lastlog 命令查看 |
| btmp | 登录失败信息，last -f /var/log/btmp |
| mail.log | 系统电子邮件服务器(sendmail)日志(Redhat maillog) |

### journalctl

systemd 是 Linux 系统中广泛使用的 init 系统和服务管理器。journalctl 可以用来查询和显示 systemd 日志（journal）的内容。

journalctl -u  命令用于查看指定单元的日志。(-u 或 --unit)，如：
```
 journalctl -u docker
 journalctl -u sshd
 journalctl -u ollama
```

显示最近 30 行，并实时跟踪
```
 journalctl -u $UNIT -n 30 -f
```

倒序显示最后 50 行
```
 journalctl -u $UNIT --reverse -n 50
```

列出所有已加载的单元
```
 systemctl list-units
```

#### rsyslog

在现代 Linux 使用 systemd 的系统中，systemd-journald 是主日志系统（二进制日志，存储在 /run/log/journal/ 或 /var/log/journal/。rsyslog 是传统的文本日志系统。

journald 通过配置可以不把日志转发给 rsyslog（默认 yes）。
```
 # /var/log/auth.log 为空
 /etc/systemd/journald.conf
 ForwardToSyslog=no
```
```
 # 修改重启
 systemctl restart systemd-journald
```

### btmp

统计攻击者 IP 排行榜
```
 last -f /var/log/btmp | awk '{print $3}' | sort | uniq -c | sort -nr | head -n 20
```

统计攻击者尝试使用的用户名
```
 last -f /var/log/btmp | awk '{print $1}' | sort | uniq -c | sort -nr | head -n 20
```

**清理**
```
 truncate -s 0 /var/log/btmp
```
