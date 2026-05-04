---
source_title: MySQL基础
categories:
- DB
- Develop
- MySQL
last_modified: '2025-07-14T02:28:23Z'
---
MySQL DBA 基础

#### 连接
```
 HN="-h10.10.128.35 -P3306"
 mysql ${HN} -uroot -p -Dmysql
```

#### 免密
```
 /etc/my.cnf
 [client]
 password = your_password
 port     = 3306
 socket   = /tmp/mysql.sock
```
```
 [mysqldump]
 user     = root
 password = your_password
```

### Creating Databases, User
```
 # drop database bi;
 CREATE DATABASE bi DEFAULT CHARACTER SET UTF8;
 CREATE USER 'bi'@'%' IDENTIFIED BY 'bi123';
 GRANT ALL PRIVILEGES ON bi.* TO 'bi'@'%';
 alter user 'bi'@'%' identified by 'bi12345';
```
 
```
 # 允许用户远程连接
 CREATE USER 'root'@'%' IDENTIFIED BY 'rootroot';
 GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
 ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'rootroot';
```
 
```
 flush privileges;
```

### 备份恢复
```
 * 数据备份(按数据库）
 UP="-u*** -p****"
 TAB="table1 table2 ..."
 # 导出结构与数据（含 drop, create）
 mysqldump ${UP} --databases db1 db2 db3 --tables ${TAB} > backup_db_20221117.sql
 # 导出数据
 mysqldump ${UP} -t db1 db2 db3 --tables ${TAB} > backup_db_20221117.sql
 * 恢复数据
 mysql ${UP} -Ddb4 < backup_db_20221117.sql
```

### system

#### Info
```
 # user
 select user from mysql.user;
 # 版本
 select @@version
 # 运行时长
 show global status like 'uptime'
 # 引擎
 show engines                              # 支持引擎
 show variables like '%storage_engine%'    # 库默认引擎
 show create table 表名                     # 表引擎
 # 连接数
 show variables like '%max_connection%'
 # set global max_connections = 1000
 show status like 'Threads%'
  : Threads_connected, 打开的连接数.
  : Threads_running, 当前并发数，一般远低于connected数值
  : Threads_connected, 跟show processlist结果相同，表示当前连接数
```
 
```
 # config values
 show variables like '%log_error%'
 show variables like '%general%';
 show binary logs;
 show variables like '%slow_query%';
 show processlist;
 show variables like '%query_cache%';
 show status like '%status%';
 show global variables like '%undo%';
```

#### 会话
```
 #当前会话提交状态
 show variables like 'autocommit';
 #会话级关闭自动提交
 set autocommit=off;
 set session autocommit=0;
 set global autocommit=0;
 show session variables like 'autocommit';
 show global variables like 'autocommit';
```
 
```
 # lock
 show OPEN TABLES where in_use > 0;
 show processlist;
 select * from performance_schema.events_statements_current;
```

#### 外键

查询外键
 ```
select   constraint_name
from     information_schema.key_column_usage
where    1=1
and      table_name = 'table_name' 
and      table_schema = 'database_name'
;
如果不手动指定外键名称，在创建表时会自动生成，命名一般类似于 表名 + _ibfk_1, _ibfk_2
删除外键
ALTER TABLE table_name DROP FOREIGN KEY fk_name;
```

临时禁止外键检查
```
 SET FOREIGN_KEY_CHECKS = 0;
```
- 作用范围仅限当前连接会话
- 设置为 1 恢复
- 设置为 0 后，可执行跨外键限制的删除、插入等操作
- 对已有外键结构本身无影响

### 基本命令
```
 #Dimension Info
 #information_schema 
 schemata
 tables
 columns
 statistics
 user_privileges
 schema_privileges
 table_privileges
 column_privileges
 character_sets
 collations
 collation_character_set_applicability
 table_constraints
 key_column_usage
 routines
 views
 Triggers
```
 
```
 ## OP
 # 删除后缩表
 # 在创建数据库的时候设置innodb_file_per_table，这样InnoDB会对每个表创建一个数据文件，然后只需要运行OPTIMIZE TABLE  命令就可以释放所有已经删除的磁盘空间
 # my.cnf 在innodb段中加入 innodb_file_per_table=1 # 1为启用，0为禁用
 # show variables like '%per_table%';
 +-----------------------+-------+
 | Variable_name         | Value |
 +-----------------------+-------+
 | innodb_file_per_table | ON    |
 +-----------------------+-------+
```
 
```
 delete from l_serv_monitor where id > 60142477;
 commit;
 # 若表很大，时间会较长
 OPTIMIZE TABLE l_serv_monitor;
```
 
```
 # truncate table tablename;
 该命令可以清空一个表里的所有数据，并归零自增ID的值。
 myisam  清空所有数据，释放表空间
 innodb  清空所有数据，不释放表空间
```

### Other
```
 ## LOG
```
 
```
 # 日志文件就在mysql的安装目录的data目录下
 show variables like 'log_bin';
 show master status;
```
 
 
 
```
 ## Base
 show databases;
 use mysql;
 show tables;
```
```
 ## LOCK
 场景一：长事物运行，阻塞DDL，继而阻塞所有同表的后续操作
 场景二：未提交事物，阻塞DDL，继而阻塞所有同表的后续操作
 场景三：在一个显式的事务中，对TableA进行了一个失败的操作
```
 
 
```
 场景一：长事物运行，阻塞DDL，继而阻塞所有同表的后续操作
 通过show processlist可以看到TableA上有正在进行的操作（包括读），此时alter table语句无法获取到metadata 独占锁，会进行等待。
```
 
```
 这是最基本的一种情形，这个和mysql 5.6中的online ddl并不冲突。一般alter table的操作过程中（见下图），在after  create步骤会获取metadata 独占锁，当进行到altering table的过程时（通常是最花时间的步骤），对该表的读写都可以正常进行，这就是online  ddl的表现，并不会像之前在整个alter  table过程中阻塞写入。（当然，也并不是所有类型的alter操作都能online的，具体可以参见官方手册：http://dev.mysql.com/doc/refman/5.6/ en/innodb-create-index-overview.html）
 处理方法： kill 掉 DDL所在的session.
```
 
```
 场景二：未提交事物，阻塞DDL，继而阻塞所有同表的后续操作
```
 
```
 通过show processlist看不到TableA上有任何操作，但实际上存在有未提交的事务，可以在  information_schema.innodb_trx中查看到。在事务没有完成之前，TableA上的锁不会释放，alter table同样获取不到metadata的独占锁。
```
 
```
 处理方法：通过 select * from information_schema.innodb_trx\G, 找到未提交事物的sid, 然后 kill 掉，让其回滚。
```
 
 
```
 场景三：在一个显式的事务中，对TableA进行了一个失败的操作
```
 
```
 通过show processlist看不到TableA上有任何操作，在information_schema.innodb_trx中也没有任何进行中的事务。这很可能是因为在一个显式的 事务中，对TableA进行了一个失败的操作（比如查询了一个不存在的字段），这时事务没有开始，但是失败语句获取到的锁依然有效，没有释放。从perfo rmance_schema.events_statements_current表中可以查到失败的语句。
```
 
```
 官方手册上对此的说明如下：
```
 
```
 If the server acquires metadata locks for a statement that is syntactically valid but fails during execution, it does  not release the locks early. Lock release is still deferred to the end of the transaction because the failed  statement is written to the binary log and the locks protect log consistency.
```
 
```
 也就是说除了语法错误，其他错误语句获取到的锁在这个事务提交或回滚之前，仍然不会释放掉。because the failed statement is written to  the binary log and the locks protect log consistency 但是解释这一行为的原因很难理解，因为错误的语句根本不会被记录到二进制日志。
```
 
```
 处理方法：通过performance_schema.events_statements_current找到其sid, kill 掉该session. 也可以 kill 掉DDL所在的session.
```
 
```
 总之，alter table的语句是很危险的(其实他的危险其实是未提交事物或者长事务导致的) ，在操作之前最好确认对要操作的表没有任何进行中的操作、没有未提交事务、也没有显式事务中的报错语句。如果有alter  table的维护任务，在无人监管的时候运行，最好通过lock_wait_timeout设置好超时时间，避免长时间的metedata锁等待。
```
