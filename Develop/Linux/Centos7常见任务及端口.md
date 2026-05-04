---
source_title: Centos7常见任务及端口
categories:
- Develop
- Linux
last_modified: '2024-05-28T03:25:42Z'
---
```
 systemctl start|enable serviceName
 systemctl stop|disable serviceName
```

系统初始化进程 systemd
- systemd 在系统启动时使用了并发的启动机制，而旧版本使用 init， 按顺序依次启动每项服务
- systemd 按需启动服务，服务被请求时会启动，使用完成后会动态将该服务关闭
- systemd 核心的概念：单元（unit）和 目标（target）
  - systemd 进程对系统的管理就是通过一个个的单元来实现的。比如服务，每一个服务都有一个对应的单元，而且每个单元都有一个配置文件，配置文件通常以 .service 作为文件名后缀，像 sshd 服务，它的配置文件就是 /usr/lib/systemd/system/sshd.service
  - 目标单元（target unit），或者简称目标（target），它们的配置文件名后缀为 .target。在 systemd 中，target 用来模拟实现系统不同的运行级别

常用的运行级别
- 3 多用户字符模式，multi-user.target
```
 /etc/systemd/system/multi-user.target.wants
```
- 5 多用户图形界面模式，graphical.target
```
 /etc/systemd/system/graphical.target.wants
```
```
 # 查看当前系统默认运行
 systemctl get-default
 # 切换运行级别
 systemctl isolate graphical.target    # 切换到图形界面
```

### 111 - rpcbind
```
 systemctl stop rpcbind.socket
 systemctl stop rpcbind
 # Warning: Stopping rpcbind.service, but it can still be activated by: rpcbind.socket
```

### 25 - postfix
```
 systemctl stop postfix
```

### 53 - dnsmasq
```
 systemctl stop dnsmasq
 # ubuntu
 systemctl stop systemd-resolved
```

### xwindows
```
 init 3
```

### ES
```
 9200
```

### oray
```
 # phddns, phdaemon
 # 16062
 /usr/sbin/oraysl -a 127.0.0.1 -p 16062 -s phsle02.oray.net:6061 -l /var/log/phddns -L oraysl -f /etc -S /etc/oray/phddns -t /bin/bash /usr/sbin/phdaemon
```

### other
```
 # 停止disable dnsmasq
 killall dnsmasq
 SN=cups
 ./rrun ip_139 “systemctl disable ${SN};systemctl stop ${SN}”
```

## Other rpcbind.socket rpcbind 111 cupsd 631 postfix 25 dnsmasq 53

# port:631 - cupsd # systemctl disable cups # systemctl stop cups Warning: Stopping cups.service, but it can still be activated by:
```
 cups.socket
 cups.path
```

# port:25 smtp # service postfix stop

chkconfig -s cups off # 或者直接删除开机服务 chkconfig –del cups

smtp：/etc/init.d/postfix stop rpc：/etc/init.d/portmap stop svrloc：/etc/init.d/slpd stop ipp：/etc/init.d/cupsd stop

chkconfig |grep on cron 0ff 1ff 2n 3n 4ff 5n 6ff cups 0ff 1ff 2n 3:on 4:off 5:on 6:off nfs 0:off 1:off 2:off 3:on 4:off 5:on 6:off nfsboot 0:off 1:off 2:off 3:on 4:off 5:on 6:off novell-zmd 0:off 1:off 2:off 3:on 4:on 5:on 6:off nscd 0:off 1:off 2:off 3:on 4:off 5:on 6:off portmap 0:off 1:off 2:off 3:on 4:off 5:on 6:off postfix 0:off 1:off 2:off 3:on 4:off 5:on 6:off powersaved 0:off 1:off 2:on 3:on 4:off 5:on 6:off random 0:off 1:off 2:on 3:on 4:off 5:on 6:off resmgr 0:off 1:off 2:on 3:on 4:off 5:on 6:off slpd 0:off 1:off 2:off 3:on 4:off 5:on 6:off
