---
source_title: Pangolin
categories:
- DB
- Develop
last_modified: '2023-12-27T14:48:29Z'
---
Paogolin 是一款轻量级的 ETL 工具，由 Adam & LLC 用 Python 开发。

git: https://github.com/ldscfe/pangolin.git

### ENV
```
 yum install python3-devel
 # db driver
 import cx_Oracle             # Oracle
 import oracledb              # Oracle DSN
 import psycopg2              # postgreSQL/Greenplum
 import pymysql               # MySQL/Doris
 import sqlite3               # SQLite
 import clickhouse_driver     # clickhouse
 import rediscluster          # Redis Cluster, redis-py-cluster
 # Kafka
 import pykafka               # kafka
```

### db.ini
```
 [mc_bidb]
 type=**Oracle**
 host=adb.uk-london-1.oraclecloud.com
 user=admin
 passwd=KVIgeHdxGfewGDwZry3KFg==
 dsn=(description= (retry_count=20)(retry_delay=3)(address=(protocol=tcps)(port=1521)(host=adb.uk-london-1.oraclecloud.com))(connect_data=(service_name=g0afda210a8192f_bidb_medium.adb.oraclecloud.com))(security=(ssl_server_dn_match=yes)))
```
 
```
 [ms_bipg]
 type=**postgreSQL**
 host=bidbpg.postgres.database.azure.com
 db=postgres
 user=ldscf
 passwd=KVIgeHdxGfewGDwZry3KFg==
```
 
```
 [**doris**_bidb]
 type=MySQL
 host=192.168.0.158
 port=9030
 db=bi
 user=bi
 passwd=80o3g9djwajSy10bx0ubGA==
```
 
```
 [mysql_bidb]
 type=**MySQL**
 host=192.168.0.83
 port=3306
 db=tidat
 user=root
 passwd=GjK1ebCT/Pw=
```
 
```
 [ch_bidb]
 type=**Clickhouse**
 host=192.168.0.182
 port=9000
 db=default
 user=default
 passwd=dQiBJglfDN0dG5EMUr89QA==
```

### mq.ini
```
 [devmq]
 type=**Zookeeper**
 host=10.10.139.18, 10.10.139.19, 10.10.139.24
 topic=l_serv_monitor_io
 #sasl=PLAIN
 #security=SASL_PLAINTEXT
 #user=
 #passwd=
```
 
```
 [dev1]
 type=**Kafka**
 host=10.10.137.19:9092, 10.10.137.20:9092, 10.10.137.18:9092
 topic=l_serv_monitor_io
```
 
```
 [kfbidb]
 type=Kafka
 host=10.10.137.188:9092,10.10.137.33:9092,10.10.137.180:9092
 topic=l_serv_monitor_io
```
 
```
 [devtlqd]
 type=Kafka
 host=192.168.0.182:9092
 topic=testTopic
```
 
```
 [devtlqdz]
 type=Zookeeper
 host=192.168.0.182
 topic=testTopic
```

### dbct

检测数据库，执行 SQL
```
 Format: dbct dbname(in db.ini) [sql=][SQL] [LINE=1] [PRE=2]
```

Option format:
- 当 SQL 中含有等号条件时，需要完整格式：sql=SQL
- LINE=1 默认显示结果返回一行（不影响 SQL 中返回行数指定）
- PRE=2 默认密码前两位加盐

### dqct

检测 MQ，查看消息
```
 Format: mqct mqname(in mq.ini) [TOPIC[.CMD]|[.{N}]]
```

Option format:
- TOPIC，为空则显示所有 Topic，否则显示该 Topic 的 offset 范围，若无则创建。
- TOPIC.{0} 显示指定偏移量的详细信息
- TOPIC.{-1} 显示最大偏移量的详细信息
