---
source_title: Clickhouse规范
categories:
- Clickhouse
- DB
- Develop
last_modified: '2022-12-29T08:07:07Z'
---
Clickhouse 数据库设计及开发规范

### 基础

每个业务系统一套 Clickhouse 环境，每套 Clickhouse 环境可以包括多个 DB 库。DB库，类似于 Oracle 用户/ Greenplum 模式

### 数据库规划

#### SYS/EXT
```
 bi              # 系统及后台使用，管理员权限使用\\
 logs            # 日志\\
 rpt             # 报表平台使用，分片+副本库\\
 ext             # 外部表（I/O）\\
```

#### 业务库
```
 ods/dw/dm       # 分片+副本库\\
 ods_p/dw_p/dm_p # 分片库\\
 ods_l/dw_l/dm_l # 本地库\\
```

### 命名规划

#### 表
- 分布表
  - 构成分布表的表，存放数据的本地表以zr, zp 开头，区别是 ods, ods_p 不同集群方式。
  - 注意：数据单条不可重复（自动舍弃行完全重复记录）
- 本地表
  - 只在本机访问，无分片、副本（需要自行同步各主机）
