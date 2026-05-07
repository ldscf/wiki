---
source_title: PostgreSQL高可用
categories:
- DB
- Develop
- PostgreSQL
last_modified: '2023-09-22T14:49:48Z'
---
介绍一种基于 pgpool-II 的方案，实现在双机条件下，pgpool-II 服务的高可用，PostgreSQL 的高可用和负载均衡等功能。

![PostgreSQL_pgpool.png](https://mwbbs.eu.org/wiki/images/e/eb/PostgreSQL_pgpool.png)

### 方案架构

在两台服务器上，分别部署 PostgreSQL 和 pgpool-II 。

PostgreSQL 通过流复制（streaming replication）实现数据同步。

pgpool-II 监控数据库集群的状态，并将用户请求分发到数据库节点上。 pgpool-II 主节点启动虚拟 IP，作为对外访服务的地址。

#### pgpool-II 服务高可用

当 pgpool-II 主节点停止后，standby 节点升级为主节点。

#### PostgreSQL 高可用和在线恢复

主数据库停止或所在服务器宕机，则进行主备切换，原主库服务器启动后自动切换为新主库的备库。

#### 负载均衡

客户端通过 pgpool-II 访问 PostgreSQL 的写请求被发送给主库，而读请求可以随机发送给主库或备库。

### 复制类型

数据库高可用架构都会用到日志。如 mysql 的 binlog，oracle 的 redo，postgresql 的 wal。原理性质整体上相近：首先记录数据库变化并通过特定算法转换成可用的文本或记录方式的文件。其次这些日志文件都是循环或覆盖进行写入的。最后都会记录数据库的所有变化，就需要进行归档，因为他们是循环或覆盖写入的，通俗的讲就是日志文件持久化。

在PG中，流复制就是通过 WAL 日志传递的方法来保证主备数据库的同步、一致。

PostgreSQL 支持物理复制（流复制）及逻辑复制2种。
- [流复制](https://www.postgresql.org/docs/current/high-availability.html)

流复制同步方式有同步、异步两种，基于实例级的复制，只能复制整个 PostgreSQL 实例，而不能基于部分库及表。

异步流复制 Primary 库产生变换，只需成功传递 WAL 日志给备库即返回 commit。^[1]^（这个描述有问题，若此，那么当备库宕机时，主库也无法完成操作。正确的应该是主库成功即提交完成。）

同步流复制是 9.1 后才有的。
- [逻辑复制](https://www.postgresql.org/docs/current/logical-replication.html)

PostgreSQL10 开始，实现了基于表级别的复制

/etc/postgresql/15/main/

### 主库配置

#### postgresql.conf
```
 listen_addresses = '*'
 wal_level = replica
 archive_mode = on
 archive_command = 'cp "%p" /u01/pgdb/db/archive/%f'
 max_wal_senders = 10
 # wal_keep_segments = 1024
 wal_keep_size = 10GB
 hot_standby = on
 shared_buffers = 1GB
 work_mem = 20MB
```
- listen_addresses - 可以按需配置网段或 IP(有些文档写成 listen_address，导致启动报错)
- wal_level - 设置流复制模式至少设置为replica
- archive_mode - 是否启用归档
- archive_command - WAL 日志归档命令，"%p"用双引号以防止目录中有空格。
```
 # 压缩的归档日志
 如果担心归档存储的尺寸，你可以使用gzip来压缩归档文件：
```

  archive_command = 'gzip < %p > /var/lib/pgsql/archive/%f'
```
 那么在恢复时你将需要使用gunzip：
```

  restore_command = 'gunzip < /mnt/server/archivedir/%f > %p'
- max_wal_senders - 最大WAL发送进程数，从库个数 <= max_wal_senders < max_connections 
- wal_keep_segments - pg_wal 目录下保留 WAL 日志的个数。每个 WAL 文件默认 16M，为保障从库能在应用归档落后时依旧能追上主库，可以设置较大一些。13.0 以后版本将 wal_keep_segments 重命名为 wal_keep_size，让用户指定 WAL 大小而不是 WAL 文件个数。
- wal_keep_size - in megabytes; 0 disables,  Valid units for this parameter are "B", "kB", "MB", "GB", and "TB".
- hot_standby - 控制在恢复归档期间是否支持只读操作，设置为 ON 后从库为只读模式
- shared_buffers - 用于共享缓冲区的内存，是由8kb大小的块所形成的数组。PostgreSQL在进行更新、查询等操作时，首先从磁盘把数据读取到内存，之后进行更新，最后将数据写回磁盘。shared_buffers可以暂时存放从磁盘读取的数据，能够让用户下次访问不需要去磁盘直接从里面读取出来，增加查询效率。shared_buffers的系统默认值通常为128MB。合理起始值为系统内存的25%或以上。
- work_mem - 写入临时磁盘文件之前，进行内部sort(order by)和hash(join)操作需要使用的内存量。min 64kB，可以设置为：(内存 * 60%)/max_connections

#### pg_hba.conf
```
 host all all 127.0.0.1/32 trust
 host all all ::1/128 trust
```
 
```
 host    all             all             0.0.0.0/0               md5
 host    replication     postgres        192.168.0.0/0           md5
```

### 备库初始化同步主库数据
```
 pg_basebackup -h 192.168.0.21 -U postgres -D /u01/pgdb/db/bidb -X stream -R -P
 -R 为流复制写入相关配置，如：生成 standby.signal 空文件，在 postgresql.auto.conf 写入连接主库配置 primary_conninfo。
```

从 12 版本开始，去掉 recovery.conf，参数并入 postgresql.conf。不再使用 standby_mode。（在 12 以前，recovery.conf 文件的存在即触发恢复模式。）

参数说明
```
 -h 指定连接的数据库的主机名或IP地址，这里就是主库的ip
 -U 指定连接的用户名，专门负责流复制的repl用户
 -F 指定了输出的格式，支持p（原样输出）或者t（tar格式输出）
 -X 表示备份开始后，启动另一个流复制连接从主库接收 WA L日志
 -P 表示允许在备份过程中实时的打印备份的进度
 -R 表示会在备份结束后自动生成recovery.conf文件，这样也就避免了手动创建。（12.0有差异）
 -D 指定把备份写到哪个目录（非空）
 -l 表示指定一个备份的标识
```

### 从库配置
1. 拷贝主库 postgresql.conf、pg_hba.conf。
1. 将 postgresql.auto.conf 内容写入 postgresql.conf（如果创建基础备份的时候，使用了 -R 参数）。
1. 修改：postgresql.conf
```
 max_connections = 110    # 从库的 max_connections要大于主库  (change requires restart)
 # recovery_target_timeline = 'latest'    # 'current', 'latest', or timeline ID，默认为 latest。
```

创建空文件
- standby.signal       # triggering files。同时存在优先，等待新的传入 WAL 并重播
- recover.signal       # 归档恢复，消耗所有 WAL 或达到恢复参数后立即停止 WAL 重播

#### 同步复制

默认是异步复制，下列参数为同步复制。
```
 # postgresql.conf
 synchronous_commit = remote_write  # off, local, remote_write, remote_apply, or [on]
 synchronous_standby_names = '*'    # '*' = all, ['']
```

## 参考
1. [PostgreSQL主备流复制——异步](https://blog.csdn.net/weixin_41840720/article/details/108125399)
