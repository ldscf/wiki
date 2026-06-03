---
source_title: Ubuntu基础
categories:
- Linux
- Ubuntu
last_modified: '2026-05-12T12:17:00Z'
---
Ubuntu（乌班图） 是由南非人马克·沙特尔沃思(Mark Shuttleworth)创办的基于Debian Linux的操作系统。名称来自非洲南部祖鲁语或豪萨语的“ubuntu"一词，意思是“人性”“我的存在是因为大家的存在"，是非洲传统的一种价值观。

Ubuntu 基于 Debian 发行版和 Gnome 桌面环境，致力于解决 Linux 难以安装、难以使用的难点，在短短几年时间里便迅速成长为从 Linux 初学者到实验室用计算机/服务器都适合使用的发行版。

Ubuntu版本编号以“年份的最后一位.发布月份”的格式命名。一般 LTS 版本在 4 月发布。如：18.04 LTS、20.04 LTS、22.04 LTS。

### SYS

#### static IP
```
 # /etc/netplan/00-installer-config.yaml
 # netplan apply
```
 
```
 network:
```

   ethernets:

     ens33:

       addresses: [192.168.47.10/24]

       routes:
        - to: default

          via: 192.168.47.2

       nameservers:

         addresses: [192.168.47.2]

   version: 2
 
```
 ## Default dhcp
 # This is the network config written by 'subiquity'
 network:
```

   ethernets:

     ens33:

       dhcp4: true

   version: 2

#### 修改网卡名称

network -> ethernets -> enp0s3 -> set-name: ens3（需要 macaddress 正确）
 ```
network:
    ethernets:
        enp0s3:
            dhcp4: true
            match:
                macaddress: 02:00:17:05:30:10
            set-name: ens3
    version: 2
```
```
 # /etc/netplan/50-cloud-init.yaml
 # 需要重启。通常情况下，netplan apply 会报错误：Cannot rename enp0s3 (enp0s3 -> ens3) at runtime (needs reboot)
```

#### DNS

若不解析域名（如 ping 不通 www.bing.com），则可能是“提供本地 DNS 缓存与名称解析服务”未启动。
```
 systemctl start systemd-resolved
 # 开机自启
 systemctl enable systemd-resolved
```
 
```
 # 一般没必要添加
 # /etc/systemd/resolved.conf
 # DNS=8.8.8.8 114.114.114.114
```

#### 常用命令安装
- ssh     : openssh-server    # desktop 版本通常不安装 ssh
- ping    : iputils-ping
- vi      : vim
- netstat : net-tools

#### 删除多余内核

通常情况下，Linux 发行版会保留当前的内核版本和上一个版本，以便救援和回滚。这样可以确保在升级到新的内核版本后，如果出现任何问题，还可以启动到较旧的、能正常工作的内核。
- /boot 目录中包括了内核镜像、initramfs 文件和引导加载配置等与 Linux 内核相关的文件。内核文件的命名通常以 vmlinuz- 或 vmlinuz. 开头，并以版本号作为结尾。
- /lib/modules 目录则存放了与已安装内核相对应的内核模块。内核模块是可以动态加载的组件，用于增强 Linux 内核的功能。每个内核版本在 /lib/modules/ 下都有自己的子目录。
```
 # 查看已安装的 Linux 内核列表，ii：表示已安装，rc：表示已删除，但配置文件仍然存在
 dpkg --list | grep linux-image
```
 
```
 uname -r                    # 当前内核版本
 apt autoremove --purge      # 自动卸载未使用的 Ubuntu 多余内核
 apt purge linux-image-5.11* # 删除指定内核
```

#### 关闭服务

##### avahi

Avahi 是 Zeroconf 规范的开源实现，允许程序在不需要进行手动网络配置的情况下，在一个本地网络中发布和获知各种服务和主机。Zeroconf(Zero configuration networking)零配置网络服务规范，是一种用于自动生成可用IP地址的网络技术，不需要额外的手动配置和专属的配置服务器。
```
 /etc/init.d/avahi-daemon stop
 -.or.-
 systemctl stop avahi-daemon
```

#### SYS Log

##### Out of memory
```
 # ubuntu 20.04L
 tail /var/log/kern.log
 *<small>Aug 28 00:15:21 udev1 kernel: [2902647.879265] Out of memory: Killed process 2812322 (svr) total-vm:8491792kB, anon-rss:8293076kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:16304kB oom_score_adj:0
```

* total-vm (总虚拟内存): 表示进程总共申请的虚拟内存空间大小。
* anon-rss (匿名常驻集大小): 表示进程当前正在使用的，且没有对应的磁盘文件的内存部分。这部分内存通常用于存放进程的代码、数据、堆栈等。
* file-rss (文件支持的常驻集大小): 表示进程当前正在使用的，且有对应的磁盘文件的内存部分。这部分内存通常用于映射共享库、mmap()映射的文件等。
* shmem-rss (共享内存常驻集大小): 表示进程当前正在使用的共享内存部分。共享内存是多个进程之间共享的一块内存区域，用于进程间通信。
* UID (用户ID): 表示进程所有者的用户ID。0 表示进程以 root 用户身份运行。
* pgtables (页表大小): 表示进程用来管理虚拟内存和物理内存映射关系的页表所占用的内存空间。
* oom_score_adj (OOM评分调整): 表示当系统内存不足时，这个进程被杀死的优先级。0表示普通优先级。*

### 20.04 -> 22.04

#### 先更新
```
 apt update          # 更新软件包列表
 apt upgrade         # 升级所有已安装的软件包
 # 或者全面升级（包括依赖关系变更） apt full-upgrade
```

#### 升级 OS
```
 do-release-upgrade  # 时间较长，需要在一个稳定的终端上操作
```

如果发现配置文件被修改过，则提醒：（默认保留，可以用 Y 覆盖修改）
 ```
Configuration file '/etc/systemd/journald.conf'
 ==> Modified (by you or by a script) since installation.
 ==> Package distributor has shipped an updated version.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
```

清理及修复
 ```
1. 清理旧版本软件包
apt autoremove         # 删除不再需要的旧版本软件包
apt autoclean          # 清理下载的软件包缓存
1. apt clean             # 彻底清理所有软件包缓存
1. 删除旧内核版本（保留当前运行版本）
apt purge $(dpkg -l | awk '/^ii linux-image-*/ && !/$(uname -r)/ {print $2}')
1. 清理保留的旧配置文件
dpkg --purge $(dpkg -l | awk '/^rc/ {print $2}')
1. dpkg -l | grep '^rc'   # 查看
1. 修复依赖关系
apt --fix-broken install
1. dpkg --configure -a    # 检查
1. 修复服务失败
systemctl reset-failed 
 # 也可以加上: systemctl daemon-reload && dpkg --configure -a && apt --fix-broken install -y
1. 查看服务失败服务 systemctl --failed
  UNIT                        LOAD   ACTIVE SUB    DESCRIPTION                           
● irqbalance.service          loaded failed failed irqbalance daemon
● networkd-dispatcher.service loaded failed failed Dispatcher daemon for systemd-networkd
... ...
```

#### Error

##### iptables

iptables/1.8.7 Failed to initialize nft: Protocol not supported.

**原因**：现代 Linux 发行版中的 iptables 工具链（版本通常是 1.8.x 及更高）默认使用的是新的 nf_tables 后端来管理防火墙规则，而不是传统的 xtables (legacy) 后端。这个错误通常意味着以下情况之一：
* 内核缺乏 nf_tables 支持：Linux 内核没有编译或加载必要的 nf_tables 模块
* iptables 链模式设置不正确：当系统默认配置为使用 nftables 但实际环境不支持时，就会抛出此错误

**解决方案**：这种情况通常是使用了阉割版的内核，如 Oracle Cloud 提供默认的 Ubuntu on OCI 的 oracle 内核，切换为 Ubuntu generic 内核就可以彻底解决问题。或者尝试使用下列方法：

强制 iptables 使用传统的 legacy 模式：
```
 update-alternatives --set iptables /usr/sbin/iptables-legacy
 update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
```

### Q/A

#### 锁屏需要密码

据说是 gnome 的 BUG(20.04 LTS)。可以尝试着取消 Privacy -> Screen Lock。

#### thinclient_drives

通常是远程桌面/瘦客户端软件自动挂载出来的目录，常见于：xrdp。
```
 # /etc/xrdp/xrdp.ini
 rdpdr=false
 drdynvc=false
```
 
```
 # /etc/xrdp/sesman.ini
 EnableFuseMount=false
```
 
```
 systemctl restart xrdp
 systemctl restart xrdp-sesman
```
 
```
 # 清除 - 需要用户未登录状态
 # umount /home/bi/thinclient_drives
 rm -rf /home/bi/thinclient_drives
```
