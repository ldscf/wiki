---
source_title: Ubuntu 通用内核
categories:
- Develop
- Linux
- Ubuntu
last_modified: '2026-02-26T09:05:38Z'
---
**将 Ubuntu on OCI 的 oracle 内核切换为 Ubuntu generic 内核**

在 Oracle Cloud Infrastructure（OCI） 上创建的 Ubuntu 实例，默认使用的是 Oracle 定制内核（例如 5.15.0-xxxx-oracle）。该内核基于 Ubuntu 官方内核进行裁剪和定制，主要目标是适配 OCI 的虚拟化环境、启动链路和实例元数据服务。

**内核限制**

内核层面未完整启用或未编译部分 Netfilter / Netlink 相关协议族和模块，从而直接导致以下功能不可用或行为异常：
1. ipset 无法正常创建和使用集合
1. nftables 无法初始化 Netlink socket（Protocol not supported）
1. nfnetlink、nfnetlink_log 等模块缺失或不可用
1. Fail2Ban 的所有基于内核防火墙的 banaction（iptables / nftables / ipset）无法工作
1. 任何依赖 Linux 内核防火墙机制的 动态 IP 封禁方案 无法正常运行

### 安装 Ubuntu 通用内核

在已完成安装 Oracle 定制内核（例如 5.15.0-xxxx-oracle）系统上，变更为通用内核。
```
 apt update
 apt install linux-image-generic linux-headers-generic
```
```
 # uname -r
 # -> 5.15.0-1081-oracle
```
```
 # HWE 内核（Hardware Enablement Kernel）
 # HWE 会随着 Ubuntu 每半年一次的发布周期进行 “滚动升级”。比如 22.04.4 发布时，从 6.2 升到 6.5。
 apt install linux-image-generic-hwe-22.04 linux-headers-generic-hwe-22.04
```

### 更新 GRUB
```
 update-grub
```

### 修改启动项

#### 查看启动菜单
```
 # 应该有类似 Ubuntu, with Linux 5.15.0-164-**generic**
 awk -F\' '/menuentry |submenu / {print $2}' /boot/grub/grub.cfg
```

#### 一次性切换

成功启动后，再把它设成永久默认。
```
 grub-reboot "Advanced options for Ubuntu>Ubuntu, with Linux 5.15.0-164-generic"
 reboot
 ...
 uname -r
 5.15.0-164-generic
```

#### 永久修改
```
 # vi /etc/default/grub
 # 原内容
 GRUB_DEFAULT=0
 GRUB_TIMEOUT_STYLE=hidden
 GRUB_TIMEOUT=0
 # 修改为
 GRUB_DEFAULT=saved
 GRUB_SAVEDEFAULT=true
 GRUB_TIMEOUT_STYLE=hidden  # 未变
 GRUB_TIMEOUT=0             # 未变
```
```
 # 更新 GRUB
 update-grub
```
```
 # 指定默认启动 generixc 内核
 grub-set-default "Advanced options for Ubuntu>Ubuntu, with Linux 5.15.0-164-generic"
```
```
 # 验证
 grub-editenv list
 # saved_entry=Advanced options for Ubuntu>Ubuntu, with Linux 5.15.0-164-generic
```
```
 reboot
```

### 删除其他

在 purge 前，确认当前运行内核为 *-generic，且 /boot 中至少保留一个 generic 内核。
```
 PACKAGES=('linux-image-*oracle*' 'linux-headers-*oracle*')
 # 查看要删除的包
 # apt list --installed "${PACKAGES[@]}"
```
 
```
 # 删除
 apt purge "${PACKAGES[@]}"
```
 
```
 # 清理不需要的依赖
 # apt autoremove
```
 
```
 # 更新引导
 update-grub
```
```
 # 检查
 awk -F\' '/menuentry |submenu / {print $2}' /boot/grub/grub.cfg
 grub-editenv list
```
