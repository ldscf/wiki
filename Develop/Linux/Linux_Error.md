---
source_title: Linux Error
categories:
- Develop
- Linux
last_modified: '2026-08-27T05:30:59Z'
---
### nsswitch.conf

Name Service Switch 是 Linux 系统中控制系统名称服务查询顺序的核心配置文件。

它决定了当系统需要查找用户密码、主机名、网络服务、组信息等系统数据库时，应该按什么顺序、去哪些数据源（如本地文件、DNS、LDAP、NIS 等）进行检索。

#### 典型配置示例

在标准的 Ubuntu / Debian 系统常见内容如下：
 ```

# /etc/nsswitch.conf
passwd:         files systemd
group:          files systemd
shadow:         files
gshadow:        files
hosts:          files dns
networks:       files
protocols:      db files
services:       db files
ethers:         db files
rpc:            db files
```

格式：数据库: 服务模块1 [条件] 服务模块2 ...

| 数据库 (Database) | 控制的系统查找行为 | 常用模块与默认查询顺序 |
|:---|:---|:---|
| hosts | 解析主机名与 IP 地址（如 ping 或 ssh 时） | files dns （先查 /etc/hosts，查不到再查 DNS） |
| passwd | 获取系统用户账号信息 | files systemd（先查 /etc/passwd） |
| group | 获取系统用户组信息 | files systemd（先查 /etc/group） |
| shadow | 获取加密的用户密码信息 | files（查 /etc/shadow） |
| services | 查找网络服务与端口对应表 | files（查 /etc/services） |

#### SSSD

接入集中式身份认证（LDAP / SSSD / Active Directory）

在企业级内网服务器或跳板机中，如果要让 Linux 支持 LDAP/域账号登录，通常会修改 `nsswitch.conf` 追加 `sss` 模块：

```text

passwd:         files sss

group:          files sss

shadow:         files sss

### YUM
 ```
yum install -y yum-utils device-mapper-persistent-data lvm2
出现几百万条如下信息：
... 
 --> 正在处理依赖关系 perl >= 5.008，它被软件包 git-1.8.3.1-25.el7_9.x86_64 需要
 --> 正在处理依赖关系 perl >= 5.004，它被软件包 perl-XML-Parser-2.41-10.el7.x86_64 需要
 --> 正在处理依赖关系 perl，它被软件包 kernel-devel-3.10.0-1127.el7.x86_64 需要
 ---> 软件包 python-libs.x86_64.0.2.7.5-94.el7_9 将被 更新
 --> 正在处理依赖关系 libssl.so.10(libssl.so.10)(64bit)，它被软件包 python-libs-2.7.5-94.el7_9.x86_64 需要
 --> 正在处理依赖关系 libcrypto.so.10(libcrypto.so.10)(64bit)，它被软件包 python-libs-2.7.5-94.el7_9.x86_64 需要
...
```

**原因分析**

以上是 YUM 在处理依赖包冲突、更新或补丁时打印出的详细过程，特别是在系统长期未更新、依赖链庞大或系统库被第三方破坏的情况下，会出现这类“过度冗长”的输出。出现这种情况通常说明系统的软件包管理环境比较复杂或已经有些紊乱。

| 原因 | 描述 |
|:---|:---|
| ① 系统版本老旧 | 比如 CentOS 7.5 一直没更新，它尝试更新大量依赖来满足依赖链 |
| ② 源不一致或有污染 | 混用了内网镜像、第三方源，或安装过非官方 rpm 包 |
| ③ 安装的这几个包需要拉一整套工具链 | 特别是 yum-utils 和 lvm2 依赖于核心组件 |
| ④ 某些系统核心组件（如 python-libs）已有残缺或不兼容版本 | 引发级联更新或替换 |
**清理缓存并重新确认环境**
```
 yum clean all
 yum makecache fast
```
