---
source_title: MySQL Error
categories:
- DB
- Develop
- MySQL
last_modified: '2025-07-06T03:25:24Z'
---
MySQL Error

#### 使用关键字做字段名

如下面 SQL 中的 condition, 这样会报错，需要加上 "`"

或者可以将所有字段都加上，如：
```
 select   id, area,`condition`, condition_code
 from     ods.t1
```

#### ERROR 1418

This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)

开启 bin-log, 函数中需要指定操作：
- DETERMINISTIC  确定的
- NO SQL         没有SQl语句
- READS SQL DATA 只是读取数据

（以上支持）
- MODIFIES SQL DATA 要修改数据
- CONTAINS SQL 包含了SQL语句

#### 1261 - Row XX doesn’t contain data for all colums

MySQL 报 1261 或 1262 错误主要是由于 sql_mode 严格设置。
- select @@sql_mode 查看目前mode类型
- select @@global.sql_mode 查看全局设置

搜索 sql_mode，将 strict_trans_tables 删除，或者设置为空

sql-mode=“NO_UNSIGNED_SUBTRACTION,NO_ENGINE_SUBSTITUTION”

重启 MySQL 服务 mysql net stop; mysql net start;

#### 2013, Lost connection to MySQL server during query

net_read_timeout 和 net_write_timeout：控制一次连接传输数据等待的时间，默认是 30s.
- max_allowed_packet: 数据传输中使用了较大的 string filed 或者 blob filed，导致超过了max_allowed_packet
- wait_timeout ：The number of seconds the server waits for activity on a noninteractive connection before closing it. 如果 MySQL 配置的比应用低，就会出现应用程序中认为一个连接还没有到回收的时间，但是 MySQL 自己判断需要强制回收了

#### ERROR 1819

Your password does not satisfy the current policy requirements
- 0 or LOW     只验证长度
- 1 or MEDIUM  验证长度、数字、大小写、特殊字符
- 2 or STRONG  验证长度、数字、大小写、特殊字符、字典文件

SHOW VARIABLES LIKE 'validate_password%';

| Variable_name | Value |
| validate_password.check_user_name | ON |
| validate_password.dictionary_file |
| validate_password.length | 8 |
| validate_password.mixed_case_count | 1 |
| validate_password.number_count | 1 |
| validate_password.policy | MEDIUM |
| validate_password.special_char_count | 1 |
set global validate_password.policy=0;

| Variable_name | Value |
| validate_password.check_user_name | ON |
| validate_password.dictionary_file |
| validate_password.length | 8 |
| validate_password.mixed_case_count | 1 |
| validate_password.number_count | 1 |
| validate_password.policy | LOW |
| validate_password.special_char_count | 1 |

#### Cannot convert string from utf8mb4 to binary

[官方已确认是 8.0.32 中的一个bug，已在 8.0.33 版本中修复。](https://bugs.mysql.com/bug.php?id=110955#:~:text=Description%3A%20Union%20all%20derived%20table%20query%20error%3A%20Warning,others%20This%20problem%20does%20not%20exist%20in%208.0.31.)

对于 8.0.32 版本
```
 show variables LIKE '%optimizer_switch%';
 set global optimizer_switch="...,derived_condition_pushdown=off";
```

该参数于 8.0.22 版本中引入，默认值为 on，表示外查询块中与派生表相关的条件会推入到派生表中。

相关：优化器处理派生表(derived table)有两种策略：
1. 将派生表合并到外查询块中
1. 将派生表物化为一个临时表

使用优化器开关 derived_merge 来控制优化器选择哪种策略。设置为 on，选择策略 1；设置为 off，选择策略 2。此开关从 5.7.6 版本时引入，默认值为 on。

**当优化器无法合并时，条件下推到派生表(derived_condition_pushdown=on)可以减少物化数据的行数，加速查询的执行。**

#### Public Key Retrieval is not allowed

mysql 8.0+ 默认使用 caching_sha2_password 身份验证机制代替原来的 mysql_native_password，而有些客户端不支持此加密方式。

更新客户端连接驱动或者修改服务端用户的身份验证机制为 mysql_native_password。
- MySQL 身份验证机制修改
```
 ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root1234';
```
- Python
```
 pip install cryptography
```
