---
source_title: Doris install - 1.2.2
categories:
- DB
- Develop
- Doris
last_modified: '2023-11-25T12:04:32Z'
---

### 环境需求

| Linux 系统 | 版本 |
|:---|:---|
| CentOS | 7.1 及以上 |
| Ubuntu | 16.04 及以上 |
| 软件 | 版本 |
|:---|:---|
| Java | 1.8 及以上 |
| GCC | 4.8.2 及以上 |
| 模块 | CPU | 内存 | 磁盘 | 网络 | 实例数量（最低要求） |
|:---|:---|:---|:---|:---|:---|
| Frontend | 16核+ | 64GB+ | SSD 或 RAID 卡，100GB+ * | 万兆网卡 | 1-3 * |
| Backend | 16核+ | 64GB+ | SSD 或 SATA，100G+ * | 万兆网卡 | 3 * |
- 在要安装 FE，Broker 的节点上提前安装 JDK 1.8 及以上环境，BE 节点不需要（1.2以后 BE 需要）
- 关闭交换分区
- 关闭防火墙
- ext4 和 xfs 文件系统均支持（1.2 以前不支持 xfs）
- 禁用 SELinux

#### 文件句柄数

# /etc/security/limits.conf 
```
 * soft nofile 65536
 * hard nofile 65536
```

#### 其它
- Doris 的元数据要求时间精度要小于 5000 ms

#### sysctl
```
 sysctl -w vm.max_map_count=2000000
```

### 安装

| IP | FE | BE | OB | Broker | Memo |
|:---|:---|:---|:---|:---|:---|
| 192.168.0.21 | 1 | 1 |
| 192.168.0.22 | 1 | 1 | 1 |
| 192.168.0.25 | 1 |
| 192.168.0.26 | 1 |

#### Java 1.8

FE、Broker 节点，从 1.2 版本开始支持 Java UDF 函数，BE 也依赖于 Java 环境。

简单说，均需要 Java 环境。

#### Doris FE
```
 xz -dk apache-doris-fe-1.2.2-bin-x86_64.tar.xz
 tar -xvf apache-doris-fe-1.2.2-bin-x86_64.tar
```
 
```
 mv apache-doris-fe-1.2.2-bin-x86_64 /opt/
 ln -s /opt/apache-doris-fe-1.2.2-bin-x86_64 /opt/doris_fe
```
 
```
 chown -R hdfs:hadoop /opt/*doris*
```

##### profile
```
 export JAVA_HOME=/usr/java/jdk1.8.0_361 
 # export DORIS_HOME=/opt/doris_fe
```

DORIS_HOME 在 conf/fe.conf 中使用，如：LOG_DIR、JAVA_OPTS**、(# meta_dir、sys_log_dir、audit_log_dir)。
```
 DORIS_HOME 不设置亦可，因为在其启动文件中，已经重设了该变量：
```
 
```
 curdir="$(cd "$(dirname "${BASH_SOURCE[0]}")" &>/dev/null && pwd)"
 ...
 DORIS_HOME="$(
```

    cd "${curdir}/.."

    pwd
```
 )"
 export DORIS_HOME
 也就是说，在 .profile 中设置了也无用（FE/BE 相同）。
```

##### conf/fe.conf
```
 JAVA_OPTS="-Xmx8192m，需要调整
 meta_dir=元数据存放位置。默认在 fe/doris-meta/ 下，改变目录需手动创建
 priority_networks=192.168.0.0/24
```
```
 http_port = 8030
 query_port = 9030
 rpc_port = 9020
 edit_log_port = 9010
```

以上配置对应下面服务：
```
 http://192.168.0.21:8030
 doris 内置默认超级管理员用户: root/NULL
```
 
```
 mysql -uroot -P9030 -h127.0.0.1
 set password for 'root' = password('root');
```

##### Start
```
 ${DORIS_HOME}/bin/start_fe.sh --daemon
 ${DORIS_HOME}/bin/stop_fe.sh
```

##### Add
```
 ALTER SYSTEM ADD|DROP FOLLOWER[OBSERVER] "fe_host:edit_log_port";
```
- 添加 FE 节点到集群(FE 端 MySQL 下执行)
```
 ALTER SYSTEM ADD FOLLOWER "192.168.0.22:9010";
```
- 添加 OB 节点到集群
```
 ALTER SYSTEM ADD OBSERVER "192.168.0.23:9010";
```

部署多个 FE 实例时，要保证 FE 的 http_port 配置相同
- 删除 FE/OB
```
 ALTER SYSTEM DROP FOLLOWER "192.168.0.22:9010";
 ALTER SYSTEM DROP OBSERVER "192.168.0.23:9010";
```

##### State
- cur http://192.168.0.21:8030/api/bootstrap
```
 {"msg":"success","code":0...}
```
- mysql> show frontends\G;
```
 Role: FOLLOWER
 IsMaster: true
 Join: true
 Alive: true
```

启动/停止脚本友好性较佳，用 root/hdfs 均可启动/停止，生成的 log/meta 均为 hdfs 权限

#### Doris BE
```
 xz -dk apache-doris-be-1.2.2-bin-x86_64.tar.xz
 tar -xvf apache-doris-be-1.2.2-bin-x86_64.tar
```
 
```
 mv apache-doris-be-1.2.2-bin-x86_64 /opt/
 ln -s /opt/apache-doris-be-1.2.2-bin-x86_64 /opt/doris_be
```
 
```
 chown -R hdfs:hadoop /opt/*doris*
```

##### profile
```
 export JAVA_HOME=/usr/java/jdk1.8.0_361
 # export DORIS_HOME=/opt/doris_be
```

由于从 1.2 版本开始支持 Java UDF 函数，BE 依赖于 Java 环境。安装Java UDF 函数需要从官网下载 Java UDF 函数的 JAR 包放到 BE 的 lib 目录下，否则可能会启动失败(be.out: Failed to initialize JNI: Failed to find JniUtil class.)。apache-doris-dependencies 包含用于支持 JDBC 外表和 JAVA UDF 的 jar 包，以及 Broker 和 AuditLoader。(倒也无需非得在 start_be.sh 中增加 export JAVA_HOME=...)

DORIS_HOME 在 conf/be.conf 中使用，如：PPROF_TMPDIR、(# storage_root_path、sys_log_dir)

##### conf/be.conf
```
 priority_networks=192.168.0.0/24
 storage_root_path 可以指定多个磁盘目录(;), 类型(medium:HDD or medium:SSD，如果混用需要指定)，以及使用的大小(,G)。默认为 ${DORIS_HOME}/storage, capacity limit is disk capacity, HDD(default)
```
```
 be_port = 9060
 webserver_port = 8040
 heartbeat_service_port = 9050
 brpc_port = 8060
```

##### Start
```
 ${DORIS_HOME}/bin/start_be.sh --daemon
 ${DORIS_HOME}/bin/stop_be.sh
```

##### Add

添加 BE 节点到集群(FE 端 MySQL 下执行)
```
 # 先添加再启动也未尝不可
 ALTER SYSTEM ADD BACKEND "be_host_ip:heartbeat_service_port";
 ALTER SYSTEM ADD BACKEND "192.168.0.22:9050";
```

删除 BE 节点

该节点的 BackendId 会发生变化，再加回来也不会是原  BackendId
```
 ~~ALTER SYSTEM DROP BACKEND "be_host_ip:heartbeat_service_port";~~
 ~~（不推荐，会直接删除BE节点，且数据不可恢复。为了安全，上面的 drop, 实际使用会有提醒使用 dropp）~~
 ALTER SYSTEM DECOMMISSION BACKEND "be_host:be_heartbeat_service_port"; 
 删除 BE 时会将该节点的数据向其他 BE 节点处进行迁移
```

##### State
- cur http://192.168.0.22:8040/api/health
```
 {"status": "OK","msg": "To Be Added"}
```
- mysql>show backends\G;
```
 Alive: true
```

#### Broker

Broker 以插件的形式，独立于 Doris 部署。如果需要从第三方存储系统导入数据，需要部署相应的 Broker，默认提供了读取 HDFS 对象存储的 fs_broker。fs_broker 是无状态的，建议每一个 FE 和 BE 节点都部署一个 Broker。
- apache-doris-dependencies-1.2.2-bin-x86_64 解压
```
 mv apache_hdfs_broker/ /opt/
 chown -R hdfs:hadoop /opt/apache_hdfs_broker
```
- 在相应 broker/conf/ 目录下对应的配置文件中，可以修改相应配置(也没啥可修改的)
```
 broker_ipc_port = 8000
```
- 启动 Broker
```
 bin/start_broker.sh --daemon
 bin/sop_broker.sh
```
- 添加 Broker  要让 Doris 的 FE 和 BE 知道 Broker 在哪些节点上，通过 sql 命令添加 Broker 节点列表。
```
 ALTER SYSTEM ADD BROKER broker_name "broker_host1:broker_ipc_port1","broker_host2:broker_ipc_port2",...;
 其中 broker_host 为 Broker 所在节点 ip；broker_ipc_port 在 Broker 配置文件中的conf/apache_hdfs_broker.conf
 ALTER SYSTEM ADD BROKER broker_name "192.168.0.21:8000";
```
- 查看 Broker 状态  使用 mysql-client 连接任一已启动的 FE，执行以下命令查看 Broker 状态：
```
 SHOW PROC "/brokers";
 Alive: true
```

### Log

log/be.out
```
 中有错误信息，若正常启动，则只有一条启动时间：
```

  start time: Tue Mar  7 14:45:45 UTC 2023
```
 停止则无记录。
```

### Error
- fe.warn.log: WARN (UNKNOWN 192.168.0.21_9010_1678188857475(-1)|1) [Env.notifyNewFETypeTransfer():2371] notify new FE type transfer: UNKNOWN

运行后修改了端口号，FE 需要将 doris-meta 目前清空。如上面信息即是将原 8030/9020 等修改后，无法启动。（仅可启动 FE-edit_log(9010)）

同样，BE 需要将文件 storage/cluster_id 删除。
