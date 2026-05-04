---
source_title: Postgresql基础
categories:
- DB
- Develop
- PostgreSQL
last_modified: '2024-05-13T07:05:12Z'
---
PostgreSQL is available in all Ubuntu versions by default.

### Install
```
 # ubuntu
 sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
 wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
 sudo apt-get update
 sudo apt-get -y install postgresql
 # centos7
 yum install postgresql-server postgresql
 service postgresql initdb
```
```
 systemctl start postgresql
```

指定版本：postgresql-12

安装完毕后，系统会创建一个数据库超级用户 postgres，密码为空。

#### 远程访问
```
 # 修改密码(原密码为空)
 ALTER USER postgres WITH PASSWORD 'postgres';
 # 修改配置文件，目录：/var/lib/pgsql/??/data/
 # postgresql.conf
 listen_addresses = '*'
 # pg_hba.conf
 host    all    all    0.0.0.0/0    md5
```

#### 将原 DB 目录移至新路径
```
 1. 修改 pg 配置文件(/etc/postgresql/15/main/postgresql.conf) 中 data_directory
 2. mv PGDB_OLD /u01/pgdb/db/，并修改目录权限
 3. export PGDATA=/u01/pgdb/db/bidb  # 不写也不影响下面语句执行。
 systemctl start postgres 相当于：/usr/lib/postgresql/15/bin/postgres -D /u01/pgdb/db/bidb -c config_file=/etc/postgresql/15/main/postgresql.conf
```

#### 使用新 DB 目录
```
 mkdir -p /u01/pgdb/db/owdb
 chown -R postgres:postgres /u01/pgdb/db/owdb
 /usr/lib/postgresql/15/bin/initdb /u01/pgdb/db/owdb
```
```
 /usr/lib/postgresql/15/bin/pg_ctl -D /u01/pgdb/db/owdb -l logfile start
 # 此方式适合新建 DB。
 # 尽量不要使用这种方式，除非是同一台机器启动多个DB。（可以用上面的将原 DB 目录移至新路径并修改配置文件方式）
```

#### Remove
```
 apt-get --purge remove postgresql*
 rm -r /etc/postgresql/
 rm -r /etc/postgresql-common/
 rm -r /var/lib/postgresql/
 rm -r /var/log/postgresql/
```

一般来说，用上面语句无法完全清除 postgresql，可以使用以下语句：
```
 apt-get autoremove postgresql
```

### DBA

#### Start
```
 # 需要 root 权限
 /etc/init.d/postgresql start | stop | restart
 systemctl start | stop | restart | status postgresql
 # 指定DB目录
 /usr/lib/postgresql/15/bin/pg_ctl -D /u01/pgdb -l logfile start
```

重载配置
```
 pg_ctl reload [-D DATADIRT]
 select pg_reload_conf();
```

#### 查看数据库信息

文件目录
```
 show data_directory;
```

查看数据库版本
```
 show server_version;
 select version();
 psql --version
```

#### 查看主备状态
```
 select * from pg_catalog.pg_stat_replication;
 sync_state = async   # 主备数据复制使用异步方式；
 state = streaming    # 流复制方式
```

#### 执行SQL
```
 psql -c "$SQL"
```

#### show databases
```
 \l
 select * from pg_database;
```
```
 # connect databases
 \c postgres
```

#### show tables
```
 \dt
 \dt+           # 表类型、大小
```

#### user
```
 create user repl REPLICATION LOGIN password 'PASSWD';
```
 
```
 # 修改密码
 alter user postgres password 'PASSWD';
```

### SQL
```
 \c postgre
 create table test (ky int, val varchar(20));
 insert into test values(1, 'hello, world!'), (2, 'Hello, postgresql.');
```

### 参考
1. [PostgreSQL 教程](https://www.runoob.com/postgresql)
1. [PostgreSQL DBA常用SQL查询语句](https://cloud.tencent.com/developer/article/1558721)
