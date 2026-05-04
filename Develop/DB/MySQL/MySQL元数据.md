---
source_title: MySQL元数据
categories:
- DB
- Develop
- MySQL
last_modified: '2023-11-10T04:00:42Z'
---
以下适用于 5.7 以上版本。

| 库名 | 表数量 | 视图数量 |
|:---|:---|:---|
| information_schema | 表、列、视图、触发器等信息，访问权限等 | 61 | 0 |
| mysql | 用户、密码、权限、关键字等信息 | 32 | 0 |
| performance_schema | 性能监控，默认关闭。在 my.cnf 中设置 performance_schema 开启 | 87 | 0 |
| sys | sys_config，系统参数配置 | 1 | 100 |

### information_schema

SET_APPLICABILITY

| 表名 | 注释 |
|:---|:---|
| SCHEMATA | 提供了当前mysql实例中所有数据库的信息。是show databases的结果取之此表 |
| TABLES | 提供了关于数据库中的表的信息（包括视图）。详细表述了某个表属于哪个schema、表类型、表引擎、创建时间等信息。是show tables from schemaname的结果取之此表 |
| COLUMNS | 提供了表中的列信息。详细表述了某张表的所有列以及每个列的信息。是show columns from schemaname.tablename的结果取之此表 |
| STATISTICS | 提供了关于表索引的信息。是show index from schemaname.tablename的结果取之此表 |
| USER_PRIVILEGES | 用户权限表:给出了关于全程权限的信息。该信息源自mysql.user授权表。是非标准表 |
| SCHEMA_PRIVILEGES | 方案权限表:给出了关于方案（数据库）权限的信息。该信息来自mysql.db授权表。是非标准表 |
| TABLE_PRIVILEGES | 表权限表:给出了关于表权限的信息。该信息源自mysql.tables_priv授权表。是非标准表 |
| COLUMN_PRIVILEGES | 列权限表:给出了关于列权限的信息。该信息源自mysql.columns_priv授权表。是非标准表 |
| CHARACTER_SETS | 字符集表:提供了mysql实例可用字符集的信息。是SHOW CHARACTER SET结果集取之此表 |
| COLLATIONS | 提供了关于各字符集的对照信息 |
| COLLATION_CHARACTER_ | 指明了可用于校对的字符集。这些列等效于SHOW COLLATION的前两个显示字段。 |
| TABLE_CONSTRAINTS | 描述了存在约束的表。以及表的约束类型 |
| KEY_COLUMN_USAGE | 描述了具有约束的键列 |
| ROUTINES | 提供了关于存储子程序（存储程序和函数）的信息。此时，ROUTINES表不包含自定义函数（UDF）。名为“mysql.proc name”的列指明了对应于INFORMATION_SCHEMA.ROUTINES表的mysql.proc表列 |
| VIEWS | 给出了关于数据库中的视图的信息。需要有show views权限，否则无法查看视图信息 |
| TRIGGERS | 提供了关于触发程序的信息。必须有super权限才能查看该表 |

### mysql

| 表名 | 注释 |
|:---|:---|
| user | 用户列、权限列、安全列、资源控制列 |
| db | 用户列、权限列 |
| host |
| table_priv |
| columns_priv |
| proc_priv |
