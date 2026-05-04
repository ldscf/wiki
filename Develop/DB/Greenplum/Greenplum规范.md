---
source_title: Greenplum规范
categories:
- DB
- Develop
- Greenplum
last_modified: '2023-01-06T06:30:35Z'
---
Greenplum 数据库设计及开发规范

### 基础

多个业务系统一个DB环境（多个库）

postgres (Greenplum Database) 6.15.0 PostgreSQL 9.4.24 

Greenplum Version: 'PostgreSQL 9.4.24 (Greenplum Database 6.18.2
- 用户/角色在整个节点所有数据库实例中是全局的。
- 用户和角色区别是用户默认有LOGIN权限。
- 每个数据库的逻辑结构对象都有一个所有者，所有者默认拥有所有权限，不需要重新赋予。
- 删除和任意修改它的权利不能赋予别人，为所有者固有，不能被赋予或撤销。
- 在系统初始化时有个预定的超级用户，操作系统用户名,比如gpadmin。

在 greenplum 后续版本中，已经用role取代了user.

---

### 库规划

为不同应用建不同名称的库，如：cbsgp, nhgp等

### ROLE/用户规划
- 管理员用户：bi
- 用户加上相应的库前缀：dtsbi, dtsdw, dtsrpt，…
- bi/*dw/*rpt只能操作相同库下的模式中的表，如：dtsbi能读写 dtsbp 下的bi/logs/ext等，dtsdw能读写 dtsgp 下的 ods/dw/dm 等模式下的表/视图/函数等

### 模式规划

每个库都建相应的模式

#### 默认模式
- bi # 系统及后台使用
- logs # 日志
- ext # 外部表（I/O）
- 以上均归属于某一个 *bi，如：dtsbi

#### 其他模式
- ods, dw, dm ETL使用，归属于某一个*dw，如：dtsdw
- rpt，报表平台使用，归属于某一个*rpt，如dtsrpt，可读其他模式（ods, dw, dm）的表

### 表规划
- 大表优先使用列式存储
- 建议不要使用压缩（Greenplum Database 6.20.0版本以前）
