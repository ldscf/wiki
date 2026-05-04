---
source_title: Fail2ban
categories:
- Develop
- Linux
last_modified: '2025-12-29T05:20:06Z'
---
Fail2Ban 是一个运行于 Linux 系统上的安全防护工具，使用 Python 编写，主要用于防止暴力破解登录、恶意扫描等行为。

通过监控系统和服务日志（如/var/log/auth.log、/var/log/apache/access.log 等），检测异常或重复失败的访问请求。一旦触发规则，Fail2Ban 会调用系统防火墙（如iptables 或nftables）临时或永久封锁攻击源 IP，从而阻止其继续访问受保护的服务。

### 安装
```
 # apt update
 apt install fail2ban
```

### 配置

Fail2Ban 默认带有一个配置文件 /etc/fail2ban/jail.conf，不建议直接修改该文件。应在同一目录下新建 jail.local，Fail2Ban 会优先加载此文件中的配置。
1. 累计失败 5 次就封禁
1. 10 分钟内发生上述失败
1. 封禁 1 小时 (如果想永久封禁，设为 -1)
1. 白名单: 所有本地回环地址、localhost、内网网段
 ```

# jail.local
 [sshd]
 enabled = true
 port    = ssh
 filter  = sshd
 logpath = /var/log/auth.log
 maxretry = 5
 findtime = 6000
 bantime  = 360000
 ignoreip = 127.0.0.1/8 ::1 192.168.1.0/24
 
 # systemctl restart fail2ban
```

### 操作
- 手动解封: fail2ban-client set sshd unbanip ${IP}
- 运行jail: fail2ban-client status
- 封禁 IP 列表: fail2ban-client status sshd
- 日志: /var/log/fail2ban.log

### Oracle 定制内核

上面的操作在 Oralce Cloud 主机 Ubuntu 22.04(5.15.0-1081-oracle) 中不报错但也未成功。
1. 可能的原因是 Oracle 定制内核未实现/禁用该协议族。
1. NETLINK_NETFILTER 协议族在运行态不可用
1. 以下均不可用：iptables 复杂功能 / 自动链 / module match / ipset / nftables / Fail2Ban
1. 内核保留了基础 iptables INPUT/FORWARD/OUTPUT 钩子，用于手工规则
```
 # 检验：内核是否支持 netlink 防火墙控制面
 modprobe nfnetlink         #
 echo $?                    # 0
 lsmod | grep nfnetlink     # nfnetlink              20480  2 nft_compat,nf_tables
```
