---
source_title: Hive install
categories:
- Develop
- Hadoop
- Hive
last_modified: '2024-12-18T08:03:34Z'
---
## Hive 4

Hive 4 comes with hive-iceberg that ships Iceberg, so no additional downloads or jars are needed. For older versions of Hive a runtime jar has to be added.

P.S. hadoop-3.4.1+Hive4.0.1 在安装 Tez(0.10.4) 上遇到问题，查询汇总等正常，但插入报错（MR均正常）。

### 安装包

[Apache Archive Distribution Directory](https://archive.apache.org/dist/hive/)

### MySQL

配置参考 Hive 3
```
 schematool -dbType mysql -initSchema
```

### 启动 Hive
```
 hive --service metastore &       # 9083
 hive --service hiveserver2 &     # 10000, 10002
```

### hive cli
```
 beeline -u jdbc:hive2://192.168.0.249:10000/ -n hdfs
 beeline
   !connect jdbc:hive2://192.168.0.249:10000/
```

### iceberg

[hive feature-support](https://iceberg.apache.org/docs/nightly/hive/#feature-support)

Hive 4.0.0 comes with the Iceberg 1.4.3 included.

### Engine

#### Spark on Hive

Spark 通过 Spark-SQL 使用 Hive 语句操作 Hive，底层运行的还是 Spark RDD。大部分情况下使用这种。
1. Spark-SQL 加载 Hive 配置文件，获取 Hive 的元数据信息
1. 通过 Spark-SQL 来操作 Hive 表中的数据

#### Hive on Spark

Hive 引擎从 Map Reduce 替换为 Spark RDD。

要使用 Hive on Spark，所用的 Spark 版本不能包含 Hive jar 包（Hive 官网: Note that you must have a version of Spark which does not include the Hive jars.）

在 spark 官网下载的编译 Spark 都是集成 Hive 的，因此需要下载源码编译，并且编译的时候不指定 Hive。

### Error

#### User: hdfs is not allowed to impersonate anonymous

$HIVE_HOME/conf/hive-site.xml
```
  
    hive.server2.enable.doAs
    false
  
```

需要重启 metastore, hiveserver2

可能需要：$HADOOP_HOME/etc/hadoop/core-site.xml
```
  
    hadoop.proxyuser.hdfs.hosts
    *
  
  
    hadoop.proxyuser.hdfs.groups
    *
  
```

需要重启 dfs, yarn

#### SLF4J: Class path contains multiple SLF4J bindings
```
 /opt/hive/lib/log4j-slf4j-impl-*.jar 删除或改名
```

## Hive3

Hive 3 新特性
- 不再支持 Mr，取而用 Tez 查询引擎，且支持两种查询模式：Container 和 LLAP
- Hive CLI不再支持（被 beeline 取代)
- SQL Standard Authorization 不再支持，且默认建的表就已经是 ACID 表
- 支持 “批查询” (TEZ) 或者 “交互式查询”(LLAP)

Hive 3 其他特性：
- 物化视图重写
- 自动查询缓存
- 会话资源限制：用户会话数，服务器会话数，每个服务器每个用户会话数限制

### 安装包
```
 # wget https://dlcdn.apache.org/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
 wget https://archive.apache.org/dist/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz
 tar -zxvf apache-hive-3.1.3-bin.tar.gz
```
```
 # 将目录移至 /opt/，建立软连接，并修改用户/组归属(如：hdfs:hadoop)
 ln -s /opt/apache-hive-3.1.3-bin /opt/hive
```

### profile
```
 #hive, 20230212, Adam
 export HIVE_HOME=/opt/hive
 export PATH=$PATH:$HIVE_HOME/bin
```
 
```
 source /etc/profile
 hive --version
```

### INIT
```
 ## Init hdfs
 init-hive-dfs.sh
 #? hive-config.sh
```

#### 使用 derby 作为资料库（默认）

Init Schema
```
 cd /opt/hive/bin
 schematool -dbType derby -initSchema
 # hive 元数据将清空，但表及数据在 hdfs 中存在（如 test 库 test 表重建后，数据可读。默认：/user/hive/warehouse/test.db/test）
 # derby 易损坏，如使用虚拟机，休眠后可能会导致库损坏，再次进入 hive 报错。
```

hive
```
 $ hive
 SLF4J: Class path contains multiple SLF4J bindings.
 SLF4J: Found binding in [jar:file:/opt/apache-hive-3.1.3-bin/lib/log4j-slf4j-impl-2.17.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
 SLF4J: Found binding in [jar:file:/opt/hadoop-3.3.4/share/hadoop/common/lib/slf4j-reload4j-1.7.36.jar!/org/slf4j/impl/StaticLoggerBinder.class]
 SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
 SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
 Hive Session ID = e15e49b7-957c-4d21-9073-84fc96a50dad
```
 
```
 Logging initialized using configuration in jar:file:/opt/apache-hive-3.1.3-bin/lib/hive-common-3.1.3.jar!/hive-log4j2.properties Async: true
 Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. Consider using a different execution engine (i.e. spark, tez) or using Hive 1.X releases.
 hive> show databases;
 FAILED: HiveException java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient
```

#### 使用 MySQL 作为资料库
 ```
1. ubuntu 直接安装：apt install mysql-server
2. 将驱动 /usr/share/java/mysql-connector-j-8.0.31.jar 拷贝到 $HIVE_HOME/lib 目录下([MySQL Connector](https://downloads.mysql.com/archives/c-j/))
3. 创建 $HIVE_HOME/conf/hive-site.xml，可以在 MySQL 中先建好 hive 库，hive 用户（密码：hive）
4. Init Schema
 schematool -initSchema -dbType mysql

# hive-site.xml
  
    javax.jdo.option.ConnectionURL
    jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true
    JDBC connect string for a JDBC metastore
  
  
    javax.jdo.option.ConnectionDriverName
    com.mysql.jdbc.Driver
    Driver class name for a JDBC metastore
  
  
    javax.jdo.option.ConnectionUserName
    hive
    Username to use against metastore database
  
  
    javax.jdo.option.ConnectionPassword
    hive
    password to use against metastore database
  
  
    hive.metastore.port
    9083
    Hive metastore listener port、
  
```

### ERR

#### INIT

mkdir:'hdfs://xxxx:9000/user/hive':No such file or directory ...... chmod: `/user/hive/warehouse': No such file or directory，需要:
1. 检查hdfs目录权限
1. 手动创建hdfs目录

derby 初始化hive仓库时如果报错:
```
 Exception in thread "main" java.lang.NoSuchMethodError: 
  com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)
 因为 hive 依赖的 juava.jar 和 hadoop 版本不一致造成的，统一成版本高的。
```
1. hive guava.jar: /opt/hive/lib
1. hadoop guava.jar: /opt/hadoop/share/hadoop/common/lib

如果先用 root 初始化，再用 hdfs 用户，也会报错。需要删除 root 创建的 derby（在 $HIVE_HOME/metastore_db ）

#### 连接

新窗口连接 hive, show databases时提示错误：FAILED: HiveException java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient 。
 ```
（1）配置hive-site.xml （在$HIVE_HOME/conf)
  
    javax.jdo.option.ConnectionURL
    jdbc:derby:/opt/hive/metastore_db;databaseName=metastore_db;create=true
  
(2) 删除原来的元数据文件夹metastore_db（在$HIVE_HOME）
mv metastore_db old_metastore_db
(3)重新初始化
schematool -dbType derby -initSchema
(4)新开窗口连接已成功，成功连接会出来一个session_id：
Hive Session ID = 1870311e-70dd-41a4-8f06-d4431190a4fb
hive> show databases;
OK
default
Time taken: 0.565 seconds, Fetched: 1 row(s)
（5）如果还不行，需要尝试删除/opt/hive/metastore_db/dbex.lck
还有的说再不行可以kill进程 ps -ef | grep RunJar
```

#### 其他

授权后再初始化安装。/opt/hive*** 文件夹授权：chown -R hdfs:hadoop /opt/hive/
1. 下载、授权等操作在root
1. 其他安装配置在hdfs
