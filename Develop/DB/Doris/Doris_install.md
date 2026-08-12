---
source_title: Doris install
categories:
- DB
- Develop
- Doris
last_modified: '2023-11-29T11:40:36Z'
---

Doris 作为一款开源的 MPP 架构 OLAP 数据库，能够运行在绝大多数主流的商用服务器上。

Doris 2.0 以上版本，fe/be/broker 等均打在一个包中。解压后目录：
- be
- extensions
  - apache_hdfs_broker
  - audit_loader
- fe

#### 环境需求

| Linux 系统 | 版本 |
|:---|:---|
| CentOS | 7.1 及以上 |
| Ubuntu | 16.04 及以上 |
| 软件 | 版本 |
|:---|:---|
| Java | 1.8 |
| GCC | 4.8.2 及以上 |

#### 开发环境​

| 模块 | CPU | 内存 | 磁盘 | 网络 | 实例数量 |
|:---|:---|:---|:---|:---|:---|
| Frontend | 2核 | 4GB | SSD 或 SATA，10GB+ | 千兆网卡 | 1 |
| Backend | 2核 | 4GB | SSD 或 SATA，50GB+ | 千兆网卡 | 1-3 |
这个配置，一次插入一百万条数据，或者连续插入每次几十万条数据，很有可能出现内存超限错误。8G 以上可支持连续插入一百万条数据，测试两千万条未出现内存超限错误。

#### 生产环境​

| 模块 | CPU | 内存 | 磁盘 | 网络 | 实例数量（最低要求） |
|:---|:---|:---|:---|:---|:---|
| Frontend | 16核+ | 64GB+ | SSD 或 RAID 卡，100GB+ * | 万兆网卡 | 1-3 * |
| Backend | 16核+ | 64GB+ | SSD 或 SATA，100G+ * | 万兆网卡 | 3 * |
- 关闭交换分区
- 关闭防火墙
- ext4 和 xfs 文件系统均支持（1.2 以前不支持 xfs）
- 禁用 SELinux

#### 文件句柄数[编辑 | 编辑源代码]

# /etc/security/limits.conf
```
 * soft nofile 65536
 * hard nofile 65536
```

立即生效

ulimit -n 65536

#### 其它[编辑 | 编辑源代码]
- Doris 的元数据要求时间精度要小于 5000 ms

#### sysctl[编辑 | 编辑源代码]
```
 /etc/sysctl.conf
 fs.file-max = 6553560
 vm.max_map_count=2000000
```
 
```
 sysctl -p
```
```
 -. OR .-
 sysctl -w fs.file-max = 6553560
 sysctl -w vm.max_map_count=2000000
```

> 注1：
1. FE 的磁盘空间主要用于存储元数据，包括日志和 image。通常从几百 MB 到几个 GB 不等。
1. BE 的磁盘空间主要用于存放用户数据，总磁盘空间按用户总数据量 * 3（3副本）计算，然后再预留额外 40% 的空间用作后台 compaction 以及一些中间数据的存放。
1. 一台机器上虽然可以部署多个 BE，但只建议部署一个实例，同时只能部署一个 FE。如果需要 3 副本数据，那么至少需要 3 台机器各部署一个 BE 实例（而不是1台机器部署3个BE实例）。多个FE所在服务器的时钟必须保持一致（允许最多5秒的时钟偏差）
1. 测试环境也可以仅适用一个 BE 进行测试。实际生产环境，BE 实例数量直接决定了整体查询延迟。
1. 所有部署节点关闭 Swap。

> 注2：FE 节点的数量
1. FE 角色分为 Follower 和 Observer，（Leader 为 Follower 组中选举出来的一种角色，以下统称 Follower）。
1. FE 节点数据至少为1（1 个 Follower）。当部署 1 个 Follower 和 1 个 Observer 时，可以实现读高可用。当部署 3 个 Follower 时，可以实现读写高可用（HA）。
1. Follower 的数量必须为奇数，Observer 数量随意。
1. 根据以往经验，当集群可用性要求很高时（比如提供在线业务），可以部署 3 个 Follower 和 1-3 个 Observer。如果是离线业务，建议部署 1 个 Follower 和 1-3 个 Observer。
- 通常建议 10 ~ 100 台左右的机器，来充分发挥 Doris 的性能（其中 3 台部署 FE（HA），剩余的部署 BE）
- 性能与节点数量及配置正相关。在最少4台机器（一台 FE，三台 BE，其中一台 BE 混部一个 Observer FE 提供元数据备份），以及较低配置的情况下，依然可以平稳的运行 Doris。
- 如果 FE 和 BE 混部，需注意资源竞争问题，并保证元数据目录和数据目录分属不同磁盘。

#### Broker 部署​

Broker 是用于访问外部数据源（如 hdfs）的进程。

#### 网络需求​

Doris 各个实例直接通过网络进行通讯，默认使用端口 80*，90*。

### 安装[编辑 | 编辑源代码]

| IP | FE | BE | OB | Broker | Memo |
|:---|:---|:---|:---|:---|:---|
| 192.168.0.121 | 1 | 1 |
| 192.168.0.122 | 1 | 1 |
| 192.168.0.123 | 1 |
| 192.168.0.124 | 1 | 1 |

#### 所有节点：

均需配置 JAVA_HOME
```
 export JAVA_HOME=/usr/java/jdk1.8.0_361
```

#### Doris FE

Java 默认使用 8G，开发环境需要调整。

若本机存在多个网络地址，则需要设置 priority_networks 指定。

conf/fe.conf
```
 JAVA_OPTS="-Xmx8192m
 priority_networks=192.168.0.0/24
```

##### Start
```
 ${DORIS_HOME}/bin/start_fe.sh --daemon
 ${DORIS_HOME}/bin/stop_fe.sh
```

##### Web

http://fe_host:http_port
```
 http://192.168.0.121:8030
 doris 内置默认超级管理员用户: root/NULL
```

状态
```
 http://192.168.0.121:8030/api/bootstrap
 {"msg":"success","code":0,"data":{"replayedJournalId":0,"queryPort":0,"rpcPort":0,"version":""},"count":0}
```

##### Add FE/OB
```
 ALTER SYSTEM ADD|DROP FOLLOWER[OBSERVER] "fe_host:edit_log_port";
```
- 添加 FE 节点到集群(FE 端 MySQL 下执行)
```
 ALTER SYSTEM ADD FOLLOWER "192.168.0.120:9010";
```
- 添加 OB 节点到集群
```
 ALTER SYSTEM ADD OBSERVER "192.168.0.124:9010";
```

部署多个 FE 实例时，要保证 FE 的 http_port 配置相同
- 删除 FE/OB
```
 ALTER SYSTEM DROP FOLLOWER "192.168.0.120:9010";
 ALTER SYSTEM DROP OBSERVER "192.168.0.124:9010";
```

##### Add BE
```
 # 先添加再启动也未尝不可
 # ALTER SYSTEM ADD BACKEND "be_host_ip:heartbeat_service_port";
 ALTER SYSTEM ADD BACKEND "192.168.0.122:9050";
```

#### Doris BE

Java 默认使用 1G，若本机存在多个网络地址，则需要设置 priority_networks 指定。

conf/be.conf

JAVA_OPTS="-Xmx1024m

priority_networks=192.168.0.0/24

##### Start

${DORIS_HOME}/bin/start_be.sh --daemon

${DORIS_HOME}/bin/stop_be.sh

##### Web

http://be_host:webserver_port

状态
```
 http://192.168.0.122:8040/api/health
 {"status": "OK","msg": "To Be Added"}
```
 
