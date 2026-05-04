---
source_title: MySQL系统优化
categories:
- DB
- Develop
- MySQL
last_modified: '2023-11-20T04:11:49Z'
---
MySQL系统优化(MySQL System Optimization)

### 连接数
- 查看最大连接数
```
 show variables like '%max_connections%'
```
- 临时设置
```
 set global max_connections = 1024;
```
- [/etc/my.cnf]
```
 [mysqld]
 max_connections = 1024
```

### [innodb_large_prefix](https://support.cpanel.net/hc/en-us/articles/4403725847959-MySQL-8-innodb-large-prefix)

MySQL innoDB 表的索引长度限制
- Error: 创建数据库表的时候，报错：ERROR 1071 (42000): Specified key was too long; max key length is 767 bytes
- MySQL 5.6 引入参数 innodb_large_prefix=ON 解决这个问题，5.7.7 作为默认值，最大限制为 3072 个字节
```
 show variables like '%innodb_page_size%'
```
- 8.0 开始，索引长度限制由表字段(row format)决定，若为 DYNAMIC 或 COMPRESSED 时，限制值为 3072；为 REDUNDANT 或 COMPACT 时，限制值为 767。且row_format=dynamic 时，长度 3072 是基于 innodb_page_size=16KB，随着 innodb_page_size 的值按比例增减。该参数只能在初始化 MySQL 实例之前配置，不能在之后修改。
```
 show variables like '%innodb_page_size%'
```
 
__TOC__