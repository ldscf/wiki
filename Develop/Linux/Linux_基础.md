---
source_title: Linux 基础
categories:
- Develop
- Linux
last_modified: '2026-08-26T02:37:30Z'
---
以下命令及参数，均在 Centos7 环境下测试通过。

### 配置

#### SSH
```
 # /etc/ssh/sshd_config
 # 生效
 sshd -t && sudo systemctl reload ssh
```

**无 22 登录**
```
 apt install ssh
 systemctl start ssh
 systemctl enable ssh
```

**允许 Root 登录**
```
 PermitRootLogin yes
```

**允许密码登录**
```
 PasswordAuthentication yes
 # ChallengeResponseAuthentication yes
 # UsePAM yes
```

**允许某用户密码登录**
```
 PasswordAuthentication no
 ...
 # 放在最后
 Match User lds
```

     PasswordAuthentication yes

     PubkeyAuthentication yes

##### SSH 证书登录
```
 # 生成私钥和公钥
 # 密码算法一般使用两种：rsa、dsa，证书登录常用的是 rsa
 ssh-keygen -t rsa
```
 
```
 # 允许密钥登录
 RSAAuthentication yes
 PubkeyAuthentication yes
```

##### SSH 禁止密码登录
```
 #/etc/ssh/sshd_config
 PasswordAuthentication no
```

Ubuntu 22.04 默认在 /etc/ssh/sshd_config 的顶部引入了配置目录：
```
 Include /etc/ssh/sshd_config.d/*.conf
```

所以
```
 grep -i "PasswordAuthentication" /etc/ssh/sshd_config.d/*.conf
```
 
```
 # Fix, 如果上面有文件 yes
 sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/g' ${FN}
```

#### DNS

##### CentOS
```
 # /etc/resolv.conf
 # systemctl restart NetworkManager    # 一般新版本不修改上面文件。
 BOOTPROTO='static' 时，网卡中DNS配置有效。
 nameserver 114.114.114.114
 nameserver 202.106.0.20
 nameserver 202.106.196.115
 nameserver 10.10.119.251
 nameserver 10.10.119.252
 nameserver 10.55.2.158
```
 
```
 domain my.com 指定本地的域名，在没有设置search的情况下，search默认为domain的值。
 search google.com bing.com ... 可用来指定多个域名，中间用空格或tab键隔开。当访问的域名不能被DNS解析时，将该域名加上search指定的参数，重新请求DNS，直到被正确解析或试完search指定的列表为止。如：ping abc不通时，继续ping abc.google.com…
```

##### Ubuntu 20.04 LTS

此版本的 DNS 需要修改：/etc/systemd/resolved.conf
```
 DNS=8.8.8.8 114.114.114.114
```
 
```
 # systemctl restart systemd-resolved   # 服务重启后覆盖 /etc/resolv.conf
```

##### 外网不通的解决办法
```
 # 设置IP和子网掩码
 ifconfig ens33 192.168.1.10 netmask 255.255.255.0
```
 
```
 #设置网关
 route add default gw 192.168.1.1
```
 
```
 #设置DNS
 # 参考上一词条
```

##### DNS 解析顺序
1. 浏览器缓存
1. 系统 DNS 缓存
1. hosts 文件
1. DNS 服务器

修改 hosts 后需要清除 DNS 缓存（以及浏览器缓存）：
 ```

# Windows
ipconfig /flushdns

# macOS
dscacheutil -flushcache

# Linux
systemd-resolve --flush-caches
```

用途：

🎯开发测试：将域名指向本地环境

🔧 调试排查：临时绕过 DNS 问题

🛡️ 屏蔽广告：将广告域名指向 127.0.0.1

⚡ 加速访问：直接指定最优 IP

#### LANG

/etc/locale.conf
```
 LANG="en_US.UTF-8"
 # LANG="zh_CN.UTF-8"
```

#### 设置系统时区为上海
```
 timedatectl set-timezone Asia/Shanghai
 # timedatectl status
 # timedatectl
 # timedatectl list-timezones
```

#### 硬件时钟

将硬件时钟调整为与本地时钟一致, 0 为设置为 UTC 时间
```
 timedatectl set-local-rtc 1
```
 
```
 # 显示当前时区
 timedatectl
```
 
```
 # 设置当前时区为东八区
 timedatectl set-timezone Asia/Shanghai
 # OR: cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
 
```
 # sync time
 yum -y install ntpdate
 ntpdate cn.pool.ntp.org
 # ntp.org 项目提供了一个全球时间服务器网络，而 pool.ntp.org 系统将负载分配到许多服务器上。使用池地址（如 cn.pool.ntp.org、0.pool.ntp.org、1.pool.ntp.org等）通常比使用单个特定服务器更好，因为它提供了冗余和负载平衡。
```

#### 网卡

network 和 NetworkManager 都可以管理和配置网络接口。NetworkManager 可以使用图形化界面。
 ```

# 禁用 NetworkManager
systemctl stop NetworkManager
systemctl disable NetworkManager

# 网卡驱动目录（一般第一个即是主网卡）
/etc/sysconfig/network-scripts/

# 状态
ip addr
ifconfig
ethtool ens1

# yum install sysstat
sar -n DEV 6
rxpck/s：每秒钟接收的数据包
txpck/s：每秒钟发送的数据包
rxbyt/s：每秒钟接收的字节数
txbyt/s：每秒钟发送的字节数
rxcmp/s：每秒钟接收的压缩数据包
txcmp/s：每秒钟发送的压缩数据包
rxmcst/s：每秒钟接收的多播数据包

# 静态 IP

# /etc/sysconfig/network-scripts/ifcfg-ens1
BOOTPROTO=static
NAME="ens1"
DEVICE="ens1"
ONBOOT="yes"
IPADDR=192.168.0.100
METMASK=255.255.255.0
GATEWAY=192.168.0.1
```

#### 修改主机名称
```
 hostname[ctl set-hostname AAA]
```

#### 创建用户
```
 # 创建目录 -m
 useradd -m bi
```

#### Firewall
```
 # 查看
 firewall-cmd --list-all
 # 防火墙操作：停止、开启、禁止、启用
 systemctl CMD firewalld.service
 CMD: start stop enable disable
```
```
 # 开放端口
 # --permanent 永久，重启后有效
 firewall-cmd --zone=public --add-port=80/tcp --permanent
 firewall-cmd --zone=public --add-port=8000-9200/tcp --permanent
 firewall-cmd --zone=public --add-port=32000-32099/udp --permanent
```
```
 # 转发
 firewall-cmd
 # 允许防火墙伪装IP
 --query-masquerade
 --add-masquerade
```
 
```
 # 80 -> 10.10.137.16:8080
 firewall-cmd --add-forward-port=port=80:proto=tcp:toaddr=10.10.137.16:toport=8080
```

##### Centos 6
```
 iptables -L -n
 iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 22
```
 

##### Centos 7
```
 IP=10.10.137.16
 iptables -I INPUT -s ${IP} ? -p tcp --dport ${PORT} -j ACCEPT
```
 
```
 iptables-save    > /etc/sysconfig/iptables  # 保存规则
 iptables-restore < /etc/sysconfig/iptables  # 重新加载规则
```
 
```
 # 配置防火墙允许指定ip访问端口
 PORT=80
```
 
```
 # 增加规则
 iptables -I INPUT -p tcp --dport ${PORT} -j DROP
```
 
```
 # 查看规则
 iptables -L
```
 
```
 # 清除所有规则
 iptables -F
```

#### 关闭 SELinux
1. SSH 证书访问需要关闭 SELinux
1. 打开可能造成 Apache 目录访问故障：[AH00686: cannot read directory for multi](https://mwbbs.eu.org/wiki/index.php/Apache#Error)
```
 # /etc/selinux/config
 SELINUX=disabled
```
```
 # getenforce/setenforce查看和设置SELinux的当前工作模式
 # 临时关闭
 setenforce 0
```

#### sudo
```
 # 将用户加入 sudo 组
 sudo usermod -aG sudo bi
```
 
```
 -.OR.-
 # export VISUAL=vim      # 改变默认编译器(nano)
 # visudo
 # 在root ALL=(ALL)   ALL下增加一行：
 root    ALL=(ALL)       ALL
 bi      ALL=(ALL)       NOPASSWD: ALL
```
 
```
 # 伪装成另一个用户运行
 CMD=gpstate
 sudo runuser -l gpadmin -c ${CMD}
```

#### 登录后自动执行
- /etc/motd

加入待显示的内容，如：Welcome to Ubuntu 20.04 LTS
- /etc/profile.d/

自动执行目录下的 sh 脚本，如：redislabs 会放入 env 脚本，不使用时需要手工移除，否则报：No such file or directory
- profile

自动执行 /etc/profile、~/.profile、~/.bash_profile 中的命令

#### journal
```
 # /var/log/journal/
 journalctl --vacuum-size=100M
 # systemctl restart systemd-journald
```

### 操作

#### 注销客户端
```
 # kill 掉某个用户的所有进程
 pkill -u oracle
```
 
```
 # w 查看登客户端 TTY
 pkill -kill -t pts/2
 # 以上带 -kill 参数为明确发送 `SIGKILL`（杀死）信号，类似于 kill -9。
```
 
```
 # killall 特征字进程
 killall test1*       # kill 掉所有 test1 开头的进程
 killall -u oracle    # kill 掉某个用户的所有进程
 killall -o 5h        # kill 掉超过 5 小时的所有进程
 killall -I XXXX      # 忽略 XXXX 大小写
 * killall -s XXX     # killall -l 查询支持的信号
 ## 使用 -i 或 --interactive 参数，来让 killkill 在停止每个进程之前提示
```
 
```
 # ps -ef | grep cloudera | grep -v grep ## | cut -c 9-15 | xargs kill -9 # 不好用，垃圾
```

#### 检查是否安装

# yum install epel-release

rpm -qa

yum list installed

pip list

dpkg -l # deb

# 如果是以源码包自己编译安装的，只能看可执行文件是否存在了，上面方法看不到这种源码形式安装的包。如果是以root用户安装的，可执行程序通常都在/sbin:/usr/bin目录下。一般用户安装在/usr/local/下，有些在/opt下

#### 后台任务

对于涉及系统级安装的脚本，使用终端复用器是最佳实践。可以关掉窗口，随时再回来查看。
```
 apt install screen
```

Example:
1. 开启一个会话：screen -S ${name}
1. << 执行任务 >>
1. 分离会话：按下 Ctrl + A，+ D
1. 查看：screen -ls
1. 恢复：screen -r ${name}（或者 pid）
1. 结束会话：exit

#### 查看linux系统是物理机还是虚拟机
```
 # lspci
 lspci | grep -i fibre       #光纤卡型号
 lspci -vvv | grep Ethernet  #万兆网卡显示为10-Gigabit
```
 
```
 # yum install dmidecode*
 dmidecode -s system-product-name
```

#### 释放内存

清理文件系统缓存
```
 sync; echo 3 > /proc/sys/vm/drop_caches
```

禁用透明大页
```
 echo never > /sys/kernel/mm/transparent_hugepage/enabled
```

内存文件 tmpfs
```
 cat /proc/meminfo | grep Shmem     # 显示 tmpfs 总占用
 df -h |grep tmpfs                  # 以挂载方式显示
```

#### OS Version

cat /etc/redhat-release

# yum install redhat-lsb

lsb_release -a

# update

# update 升级所有包，改变软件设置和系统设置,系统版本内核都升级

# upgrade 升级所有包，不改变软件设置和系统设置，系统版本升级，内核不改变

#### info

cat /proc/cpuinfo

cat /proc/meminfodmidecode -s system-product-name

#### 显示分区基本信息

fdisk -l

lsblk -f

#### 挂载
```
 parted /dev/sdb(mkpart)
 -.OR.-
 fdisk /dev/sdb(p,n/p/...,w,q)
```
```
 # 对于 vmware 主机，可能需要重启后生效
 *```
```

命令(输入 m 获取帮助)：w

The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: 设备或资源忙.

The kernel still uses the old table. The new table will be used at

the next reboot or after you run partprobe(8) or kpartx(8)

```*
 
```
 mkfs.xfs /dev/sdb1
 mkdir /u01
 mount /dev/sdb1 /u01
```
 
```
 # 启动项 /etc/fstab
 /dev/sdb1   /u01  xfs   defaults   0 0
```

#### 测试硬盘速度
```
 dd if=/dev/zero of=/tmp/test bs=1M count=10240
 dd if=/dev/zero of=/tmp/test bs=4096 count=2621440
```

# 检查 centos 服务开启情况

chkconfig --list

chkconfig --del xxx

chkconfig --level 35 xxx on/off

#centos图形化界面gnome-shell卡死的解决方法

service gdm stop

kill -9  xargs         # gnome-shell的进程

# 查看启动项

systemctl list-unit-files

更改链接/usr/share/zoneinfo目录中的时区来更改时区 /etc/localtime。

rm -rf /etc/localtime

ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# /etc/localtime -> ../usr/share/zoneinfo/Asia/Shanghai

# link

如果用 Transmit(v5.82) + Sublime Text(v4121)修改，则硬连接会失效——因为是把源文件删除后重建的，所以会有两个版本的文件。

用 vi/WinSCP(v5.1.5)+UltraEdit-32(v13.20)无此问题。

# 时间同步

# yum install -y ntpdate

ntpdate -u asia.pool.ntp.org

# BIOS时间

hwclock -r

# 回写BIOS时间

hwclock -w

#### # /dev/shm

这个目录是linux下一个利用内存虚拟出来的一个目录，这个目录中的文件都是保存在内存中

# df -h

tmpfs                 32G   84K  32G   1% /dev/shm

# free

            total        used        free      shared  buff/cache  available

Mem:      65808632      762204    64486736       58288      559692   64521436

Swap:      16777212           0   16777212

# vi /etc/fstab

tmpfs   /dev/shm  tmpfs  defaults,size=32g 0 0

mount -o remount tmpfs

# 检查端口 nmap

# (.188:/data/ndb) ./lrun ip_bo "nmap %VAR% -p 10-61000"

# TCP

# HOST=10.10.137.33

nmap ${HOST} -p 10-33000

# UDP

nmap ${HOST}  -sU -Pn -p 10-200

-sU：表示udp scan ， udp端口扫描

-Pn：不对目标进行ping探测（不判断主机是否在线）（直接扫描端口）

对于udp端口扫描比较慢，扫描完6万多个端口需要20分钟左右

# 查看目录

tree . -L 2

#### # 加密

openssl des3 -salt -k PASSWD

openssl des3 -d -k PASSWD

# tar -zcvf - ${FILEPATH) | openssl des3 -salt -k ${PASSWD} | dd of=${FILEPATH).des3

# dd if=${FILEPATH}.des3 |openssl des3 -d -k ${PASSWD} | tar zxf -

# dd if=${FILEPATH}.des3 |openssl des3 -d -k Bidb1123 > ${FILEPATH}

### 资源

#### 按内存排序
```
 top （然后按下M，注意大写）
 ps aux | sort -k4nr | head -n 10
 ps aux --sort=-%mem | head -n 10
```
```
 # 包堆积占用的内存，top 并不显示
 netstat -anput | sort -k 2 -r |head -n 20
```
```
 # apt install smem
 smem -t -u -k      # 按用户显示总内存占用
 smem -t -U hdfs -k # 显示用户进程内存占用
```

名词解释
```
 Recv-Q : 接收队列，Q 是 Queue 的缩写。表示收到的数据已经在本地接收缓冲，但还没有被进程取走
 Send-Q : 发送队列。对方没有收到的数据或者说没有 Ack 的，还在本地缓冲区
 VSZ (Virtual Set Size)     : 进程占用的虚拟内存大小（单位 KB，下同），包括尚未被物理内存实际映射的部分
 RSS (Resident Set Size)    : 进程当前驻留在 RAM 中的物理内存总量
 PSS (Proportional Set Size): 所有共享库和代码的实际占用的内存总量，按照每个进程实际使用的内存进行分配
 USS (Unique Set Size)      : 进程独自使用的内存总量，不包括共享库和代码的内存。USS 是进程私有的部分占用的内存
```

#### 按 CPU 排序
```
 ps -aux | sort -k3nr | head -n5
 top （然后按下P）
```

#### 磁盘IO
```
 # 如果 %util 接近 100%，I/O请求过多，磁盘I/O已经满负荷
 iostat -X 6
 iostat -m 6
 iotop
```

#### CPU
```
 sar -u 1 5
 sar -P ALL -u 1 5
 sar -P 0 -u 1 5
```

#### Network
```
 iptop -i ens3 -n -P
 sar -n DEV 6
```
 
```
 # 显示路由路径
 tracepath
```

   -n: 不解析主机名，直接显示IP地址

   -l: 设置初始数据包长度，默认为65535
```
 traceroute    # 需要 root 权限
```
 
```
 vnstat -h
 vnstat -d
 # 可以监控的可用接口
 vnstat --iflist
 # 选择要监控的接口
 vnstat -u -i eth0
```
```
 # 测试两台服务器之间的传输速度
 # Install iperf
 iperf -s                  # server
 iperf -c $SERVER          # client
```

#### 查看目录占用空间
```
 du -h / --max-depth=1
 # 隐藏目录 .[!.]*
```

#### 查看端口占用
```
 netstat -lnp
 lsof -i :80
 lsof -i :80-1000
```

#### 查看已删除未释放文件

一般是 df -h 与 du -ms /* 差距很大时，可以检查已删除未释放文件。一般在删除文件时，如果有进程打开了这个文件，一直未关闭此文件句柄，那么就不会释放该文件。
```
 lsof -n |grep deleted
```

查出后，关闭进程即可释放文件。

#### 进程
```
 ps -ef|grep 'XX X'
```
 
```
 # 后台进程
 nohup CMD &
 jobs
 Crtl+Z
 fg/bg
 kill %N
```

#### 动态链接库依赖项
 ```
1. 检查动态链接库或可执行文件的依赖项
ldd libldur.so
ldd server_demo
	linux-vdso.so.1 (0x00007fff817f0000)
	libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007fb96bf89000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fb96bf6e000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fb96bf4b000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fb96bd59000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fb96bc0a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb96c1d5000)
1. 检查依赖
ldd server_demo | grep "not found"
```

### CMD

#### ping
```
 ping $IP
```
  
```
 # 测试 MTU（最大傳輸單元）
 ping -s 1472 -M do 192.168.0.22
 - 如果 MTU 限制較小，數據包無法傳輸，將會收到以下報錯：
```

  ping: local error: Message too long, mtu=XXXX
```
 # 实际传输需要加上 ICMP 協議頭（8 字節）和 IP 協議頭（20 字節）
 # 典型的以太網 MTU = 1500 = 1472（data）+ 8(ICMP) + 20(IP)
 # -M do: 強制 不分片（Don't Fragment，DF 標誌）
```
 
```
 # 調整 MTU
 ip link set dev ens160 mtu 1500
```

#### rz/sz 上传文件
```
 # 超大文件传输会有问题
 yum install lrzsz
```

#### strings
```
 查看 lib 文件包内容
 strings /lib64/libstdc++.so.6 |grep CXXABI_
```

#### Swap
```
 swapoff -a      # off
 swapon -a       # on
 swapon -s       # list
```
 
```
 # SWAP文件
 dd if=/dev/zero of=/u01/swap/mem1 bs=1M count=1024
 mkswap /u01/swap/mem1
 chmod 600 /u01/swap/mem1
 swapon /u01/swap/mem1
```
 
```
 # 增加 /etc/fstab
 /u01/swap/mem1 swap swap defaults 0 0
```

#### CP
```
 -p 保留原文件时间
 -a 保留属性
```

#### SCP

默认拷贝软连接
```
 -p 保留原文件的修改时间，访问时间和访问权限
 -r 递归复制整个目录
 -v 详细方式显示输出
 -q 不显示传输进度条
 大写：
 -P port 指定目标端的 SSH 端口号
 -C 允许压缩（将-C标志传递给ssh，打开压缩功能）
```
 
```
 scp -P 32024 -rp * bi@193.123.232.187:/data/dump/.
```
 
```
 scp root@mc3:/home/bi/Downloads/W* .
 # Error: zsh: no matches found
 # 原因: zsh 试图将 * 通配符展开，但在本地却未找到对应文件，于是出现“no matches”的错误。
 # 解决：
 #  1. 在 .zshrc 中增加 setopt nonomatch，让 zsh 匹配失败时不报错并使用原本内容。
 #  2. 将 * 号部分用引号引起来，或者用 \ 阻止 zsh 转义
```

#### rsync 同步

**服务端和客户端均需安装**
```
 rsync -e 'ssh -p 32024' -rvlt /var/www/html/work_bk bi@193.123.232.187:/var/www/html/
```
 
```
 -r 递归，复制目录
 -v 显示复制的过程
 -l 同步软连接需要指定
 -t 基于文件的修改时间进行对比，只同步修改时间不同的文件
 -a 保持所有文件属性，如果文件属性变了，认为是不同文件 
 -u 选项忽略重复的数据
```
 
```
 --exclude 'name1'           排除目录或文件
 --exclude-from='e.lis'      排除 e.lis 中指定的目录或文件
```
 
```
 ----filter=':- .gitignore'  接近 Git”的方式解析规则
 --delete                    完全对齐（删除远端已存在但本地没有或被忽略的文件）
```

Example:
```
 # 将远程 mc2 主机 /udefcp 目录下的所有文件、子目录均同步至本地的 /udefcp 目录
 rsync -rlt bi@mc2:/udefcp/ /udefcp
 # 将远程 mc2 主机 /udefcp 目录下的所有文件、子目录均同步至本地的 /include/udefcp 目录
 rsync -rlt bi@mc2:/udefcp /include
```
 
```
 # 将 SRDS 目录下所有文件同步到远程 SRDS 目录
 # 比较文件内容，忽略时间
```
 
```
 cd SRDS      # 如果在下面指定目录：./ -> SRDS/，那么 filter 中要加目录
```
 
```
 rsync -avc --no-owner --no-group \
```

   --filter=':- .gitignore' \

   --filter=':- */.gitignore' \

   --exclude='.gitignore' \

   ./ bi@mc3.en:/home/bi/github/SRDS/

P.S. 源目录最后有没有 / 很关键，目标目录最后有没有 / 均可
* 源路径 SRDS/（带斜杠）：同步目录下的内容。
* 源路径 SRDS（不带斜杠）：会将整个 SRDS 目录本身 同步到目标目录下。

#### chattr

防止文件被修改(包括 root)
```
 chattr +i ~/.ssh/authorized_keys
 lsattr *        # 查看
 ----i---------e----- authorized_keys
```

只能追加数据，不能删除

  chattr +a /var/log/messages

#### tree
```
 # apt install tree
```
 
```
 # 查看指定目录
 tree -L 1 /var
```
 
```
 # 查看当前目录，4 级，过滤某些目录
 # --dirsfirst  : 结果中目录优先
 # -F           : 增加文件类型标识（目录加 /，可执行文件加 *）
 tree -L 4 -I 'dist|target|src/test|__pycache__|.*' --dirsfirst -F
```

#### comm
```
 comm [-123i] T S
 比较 T、S 两个文件，按排序结果顺序比较，出现不同即终止。结果显示成三列(1-只出现在 T，2-只出现在 S，3-均有)
 i 不区分大小写
 -n 去掉 n 列
```

#### iconv

编码转换
```
 # -c  Silently discard characters that cannot be converted instead of terminating when encountering such characters.
 iconv -f utf-8 -t gb18030 README.md -o README_gb.md
```
 
```
 # 查看文件编码
 file README.md
 -> README.md: Unicode text, UTF-8 text
 file README_gb.md
 -> README_gb.md: ISO-8859 text
```

### tmp

#### CentOS 查看系统信息

# 进程

ps -ef # 查看所有进程

top # 实时显示进程状态(M=memory sort, P=CPU sort)

# 用户：

w # 查看活动用户

id <用户名> # 查看指定用户信息

last # 查看用户登录日志

cut -d: -f1 /etc/passwd # 查看系统所有用户

cut -d: -f1 /etc/group # 查看系统所有组

crontab -l # 查看当前用户的计划任务

#### # rsz为实际内存占用

ps -e -o 'pid,comm,args,pcpu,rsz,vsz,stime,user,uid' | sort -nrk5

# sync

如果必须停止系统，则运行sync 命令以确保文件系统的完整性。sync 命令将所有未写的系统缓冲区写到磁盘中，包含已修改的 i-node、已延迟的块 I/O 和读写映射文件

# drop_caches

# 系统默认值为0，修改为1、2、3会立刻释放内存，但无法改回0.

drop_caches的详细文档如下：

Writing to this will cause the kernel to drop clean caches, dentries and inodes from memory, causing that memory to become free.

# To free pagecache:
* echo 1 > /proc/sys/vm/drop_caches

# To free dentries and inodes:
* echo 2 > /proc/sys/vm/drop_caches

# To free pagecache, dentries and inodes:
* echo 3 > /proc/sys/vm/drop_caches

As this is a non-destructive operation, and dirty objects are notfreeable, the user should run "sync" first in order to make sure allcached objects are freed.

This tunable was added in 2.6.16.

#回收Swap Used

sync

cat /proc/sys/vm/drop_caches     # 可以设为1释放内存

echo 1 > /proc/sys/vm/drop_caches

# 关闭所有交换分区

swapoff -a     # 最好后台执行

# 启用

swapon -a

# 启动指定 swap 设备

swapon /dev/mapper/VolGroup00-LogVol01

# 查看当前启用的 swap 设备

swapon -s

#### # swappiness

# 默认 vm.swappiness 值是60。将值改小，可以降低系统对于swap的写入。而将值设为0，并不会禁止对swap的使用，而是使系统对于 swap 的写入尽可能的少（当剩余空闲内存低于vm.min_free_kbytes limit时，使用交换空间）。

cat /proc/sys/vm/swappiness -OR- sysctl -q vm.swappiness

sysctl vm.swappiness=0         # 临时起作用，重启后恢复

# /etc/sysctl.conf

vm.swappiness=10

#测试硬盘速度

dd if=/dev/zero of=/tmp/test bs=1M count=10240

dd if=/dev/zero of=/tmp/test bs=4096 count=2621440

#块设备信息

# yum install util-linux-ng

lsblk -t

#解决在Linux下面使用sqlplus，上下键，退格键都不能用

# alias sqlplus='rlwrap sqlplus'

rlwrap sqlplus / as sysdba

# mount

# /etc/fstab

# 显示已挂载目录

mount

#以下 mount 远程主机 出现过 df 不了的故障

#mount -t nfs -o vers=3,proto=tcp,nolock m01.bg.5i5j.club:/ /mnt/hdfs

# iconv 编码转换

# -c     Silently discard characters that cannot be converted instead of terminating when encountering such characters.

iconv -f utf-8 -t gbk in.txt > out.txt

#硬连接，只适合于文件

# ln source target

#软连接，包括目录

ln -s source target

#硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，起到防止“误删”的功能——因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。

#硬链接说白了就是一个指针，指向文件索引节点，系统并不为它重新分配新的inode。可以用ln命令来建立硬链接。尽管硬链接节省空间，也是Linux系统整合文件系统的传统方式，但是存在一下不足之处：（1）不可以在不同文件系统的文件间建立链接（2）只有超级用户才可以为目录创建硬链接。 

#软链接: 软链接克服了硬链接的不足，没有任何文件系统的限制，而且任何用户都可以创建指向目录/文件的符号链接。因而现在更为广泛使用，它具有更大的灵活性，甚至可以跨越不同机器、不同网络对文件进行链接。

# 过滤 find warm

find / -name pgbench 2>/dev/null

# 系统日志文件

/var/log/message 系统启动后的信息和错误日志，是Red Hat Linux中最常用的日志之一

/var/log/secure 与安全相关的日志信息

/var/log/maillog 与邮件相关的日志信息

/var/log/cron 与定时任务相关的日志信息

/var/log/spooler 与UUCP和news设备相关的日志信息

/var/log/boot.log 守护进程启动和停止相关的日志消息

# 系统信息

uname -a # 查看内核/操作系统/CPU信息

cat /etc/issue

cat /etc/redhat-release # 查看操作系统版本

cat /proc/cpuinfo # 查看CPU信息

hostname # 查看计算机名

lspci -tv # 列出所有PCI设备

lsusb -tv # 列出所有USB设备

lsmod # 列出加载的内核模块

env # 查看环境变量

# 资源

free -m # 查看内存使用量和交换区使用量

df -h # 查看各分区使用情况

du -sh <目录名> # 查看指定目录的大小

grep MemTotal /proc/meminfo # 查看内存总量

grep MemFree /proc/meminfo # 查看空闲内存量

uptime # 查看系统运行时间、用户数、负载

cat /proc/loadavg # 查看系统负载

# 磁盘和分区

mount | column -t # 查看挂接的分区状态

fdisk -l # 查看所有分区

lsblk -f  # 文件系统类型

swapon -s # 查看所有交换分区

hdparm -i /dev/hda # 查看磁盘参数(仅适用于IDE设备)

dmesg | grep IDE # 查看启动时IDE设备检测状况

# 网络

ifconfig # 查看所有网络接口的属性

iptables -L # 查看防火墙设置

route -n # 查看路由表

netstat -lntp # 查看所有监听端口

netstat -antp # 查看所有已经建立的连接

netstat -s # 查看网络统计信息

# 服务

chkconfig –list # 列出所有系统服务

chkconfig –list | grep on # 列出所有启动的系统服务

# 程序

rpm -qa # 查看所有安装的软件包

# 解压bz2包

tar -jxvf extundelete-0.2.4.tar.bz2

# 列目录内容

tar -tzvf *tar.gz
