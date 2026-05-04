---
source_title: Pgpool-II
categories:
- DB
- Develop
- PostgreSQL
last_modified: '2023-09-03T07:43:53Z'
---
Pgpool-II 使用 PostgreSQL 的前、后端协议，并在后端和前端之间中继消息。数据库应用程序（前端）认为 Pgpool-II 是 PostgreSQL 服务器，而服务器（后端）将 Pgpool-II 视为客户端。

由于 Pgpool-II 对服务器和客户端都是透明的，因此现有的数据库应用程序几乎可以无需更改地与 Pgpool-II 一起使用。

Pgpool-II is a middleware that works between PostgreSQL servers and a PostgreSQL database client. It is distributed under a license similar to BSD and MIT. It provides the following features.
* **Connection Pooling**
* **Replication**
* **Load Balancing**
* **Limiting Exceeding Connections**
* **Watchdog**
* **In Memory Query Cache**

### Stable versions

[Pgpool-II](https://www.pgpool.net/mediawiki/index.php/Downloads): 4.4, 4.3, 4.2, 4.1, 4.0
- [Pgpool-II Redhat](https://www.pgpool.net/yum/rpms/)

### 同步模式

#### 内置复制
- 写操作直到所有 PostgreSQL 服务器完成写操作后才返回，对写模式性能有损耗
- 读操作可以发送任意一台，并不是随机分发的；可以通过 show pool_nodes 查看，可以实现读的负载均衡
- 复制级别是数据库集
- 该模式下 pgpool 相当于连接池

#### 流复制
- 基于 wal 日志复制。主库产生 wal 日志并发送给备库；备库接收 wal 日志记录；并重放这些 wal 日志。从而达到主备库数据同步。备库只读
- 读写查询智能分发，可以实现负载均衡，这是其他的HA软件不具有的
- 复制级别是实例级
- 该模式下 pgpool 相当于连接池

## 参考
1. [What is Pgpool-II?](https://www.pgpool.net/mediawiki/index.php/Main_Page)
1. [Installing Pgpool-II](https://www.pgpool.net/docs/latest/en/html/install-pgpool.html)
1. [pgpool-II安装](https://www.cnblogs.com/lottu/p/14069422.html)
1. [pgpool-II 4.3 中文手册 - 入门教程](https://www.cnblogs.com/hacker-linner/p/16173012.html)
