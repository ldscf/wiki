---
source_title: SQL Server概述
categories:
- DB
- Develop
- SQLServer
last_modified: '2023-05-18T11:50:46Z'
---
### 高可用
- SQL Server 2012以后，每个节点独立存储一份数据库的副本，副本可设置为可读，当主节点故障时，副本可自动接管主节点的服务（10秒内完成）。类似于 Oracle DG
- 前期版本，需要共享存储磁盘阵列

例如：使用三台服务器来实现 AlwaysOn 高可用性组，DC、DB1、DB2。
1. 要实现故障转移，首先必须在DB1和DB2上安装“故障转移集群”功能
1. 在DC上创建一个共享目录，设置SQLAdmin用户对此目录具有读写权限，此目录涉及集群的仲裁，故不应该创建在故障转移集群中。
1. 安装WSFC群集组件，配置WSFC，为所有节点均安装完“故障转移群集”服务后，在任意节点服务器的“服务器管理器”中展开“故障转移群集管理器”对WSFC进行配置。
1. AlwaysOn 可用性组和 AlwaysOn 故障转移群集实例将 WSFC 用作一种平台技术，将组件注册为 WSFC 群集资源。相关的资源将合并为一个“资源组”，这些资源可能依赖于其他 WSFC 群集资源。这样，WSFC 群集服务就可以感测并标明是否需要重新启动 SQL Server 实例，或自动将其故障转移到 WSFC 群集中的不同服务器节点上。
1. “可用性组”是一组共同实现故障转移的用户数据库。一个可用性组包含一个主"可用性副本"和一至四个辅助副本，这些副本通过基于 SQL Server 日志的数据移动来实现数据保护以进行维护，无需共享存储。每个副本均由 WSFC 群集的不同节点上的 SQL Server 实例承载。可用性组和相应的虚拟网络名称注册为 WSFC 群集中的资源。
1. 以上，需要在 windows 上安装 DNS 及 AD(Active Directory)域服务

### 镜像集群三种运行模式
- 高性能模式：principal 与 mirror 之间数据异步传输，principal 上的事务提交无需等待 mirror 的响应，principal 宕机后，存在数据更新丢失的可能，不支持自动故障转移，可以通过强制服务的方式使得 mirror 提供服务。适合对数据可靠性要求不高，性能要求较高的业务场景，与 MySQL 的异步复制模式，Oracle DataGuard 最大性能模式相近；
- 不带故障转移的高安全模式：principal 上所有的事务提交，都必须要确认该事务涉及的事务日志均已经传送到的 mirror 上，并写入 mirror 的重做队列中，持久化（是否持久化到外存设备还与 windows 操作系统写入缓存策略相关），mirror 返回确认后，才可提交，可以实现 principal 宕机下数据“零”丢失，不支持自动故障转移，可以通过手动转移或者强制服务方式使得 mirror 提供服务。与 MySQL 5.7 Loss-less replication、Oracle DataGuard 最大可用模式相近；
- 带故障转移的高安全模式：与不带故障转移的高安全模式相比，增加了 witness (见证服务器)，可以实现自动的故障转移，通过 witness，可以确保只有一个节点成为 principal，对外提供服务，实际上 witness 最重要的一个作用就是选主；

### 基本指标
- 关系数据库大小：除 Express(10G)，其他均为 524 PB
- Enterprise > BI > Standard > Web > Express
- Analysis Services(SSAS，支持 MLOAP、RLOAP、HLOAP)，安装时只可选：多维及数据挖掘模式，另一个“表格模式”无法选。
- Reporting Services
- Integration Services(SSIS)
- Master Data Services(主数据服务)
