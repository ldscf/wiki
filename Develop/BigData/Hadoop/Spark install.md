---
source_title: Spark install
categories:
- Develop
- Hadoop
- Spark
last_modified: '2024-12-12T03:19:15Z'
---
Apache Spark是一个开源集群运算框架，于 2009 年诞生于加州大学伯克利分校 AMPLab，2013 年被捐赠给 Apache 软件基金会，2014 年 2 月成为 Apache 的顶级项目。相对于 MapReduce 的批处理计算，Spark 可以带来上百倍的性能提升，因此它成为继 MapReduce 之后，最为广泛使用的分布式计算框架。

### 安装包
```
 wget https://dlcdn.apache.org/spark/spark-3.3.2/spark-3.3.2-bin-hadoop3-scala2.13.tgz
 tar -xzvf spark-3.3.2-bin-hadoop3-scala2.13.tgz
 mv spark-3.3.2-bin-hadoop3-scala2.13 /opt/
 ln -s /opt/spark-3.3.2-bin-hadoop3-scala2.13 /opt/spark
 chown -R hdfs:hadoop /opt/spark*
```

Spark 版本中的 without-hadoop 具有误导性，spark-3.5.3-bin-without-hadoop.tgz 并非指不需要 hadoop，而是不包含 hadoop 所有的 jar 包，因此需要手工指定路径。
```
 # spark-env.sh
 set SPARK_DIST_CLASSPATH = "%HADOOP_HOME%/etc/hadoop/*;%HADOOP_HOME%/share/hadoop/common/lib/*;%HADOOP_HOME%/share/hadoop/common/*;%HADOOP_HOME%/share/hadoop/hdfs/*;%HADOOP_HOME%/share/hadoop/hdfs/lib/*;%HADOOP_HOME%/share/hadoop/hdfs/*;%HADOOP_HOME%/share/hadoop/yarn/lib/*;%HADOOP_HOME%/share/hadoop/yarn/*;%HADOOP_HOME%/share/hadoop/mapreduce/lib/*;%HADOOP_HOME%/share/hadoop/mapreduce/*;%HADOOP_HOME%/share/hadoop/tools/lib/*"
```

### profile
```
 export SPARK_HOME=/opt/spark
 export PATH=$SPARK_HOME/bin:$SPARK_HOME/sbin:$PATH
```

### Configure

#### spark-env.sh
```
 export JAVA_HOME=**/usr/java/jdk1.8.0_361**
 export SPARK_MASTER_WEBUI_PORT=**8070**
 export SPARK_WORKER_WEBUI_PORT=**8071**
 # export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=**zk1:2181,zk2:2181,zk3:2181** -Dspark.deploy.zookeeper.dir=/spark"
```

#### workers

加入 Worker 节点
```
 g2-hdfs-01
 g2-hdfs-02
 ..
```

### Start

在某节点启动 spark 集群，执行 start-all.sh 命令后，实际上是在该节点上先启动 Maser 服务，然后启动在 works 配置文件中配置的所有节点上启动 Worker 服务（可能包括 localhost 节点，一般 workers 默认配置）

一般来说，如果节点上只有 Spark 服务的话，上述命令也没什么问题。但碰巧 hadoop(hdfs) 也是同样的命令。所以可以执行如下命令分别启动服务：
1. start-master.sh
1. start-workers.sh

P.S. 只是这样情况，不需要 zookeeper

在节点上启停: stop-workers.sh（启动可以使用 master 上的 start-workers.sh）

master-standby

在其它 standby 节点执行 start-master.sh 命令，会启动备用的 Master 服务（master-standby）

前题条件：spark-env.sh 中配置了 zookeeper

### SYNC
```
 HN=esxi-mc1
 FN=spark_332.tar.gz
 FP=spark-3.3.2-bin-hadoop3-scala2.13
 ssh ${HN} 'cd /tmp; tar -xzvf ${FN};  mv ${FP} /opt/; ln -s ${FP} /opt/spark; chown -R hdfs:hadoop /opt/spark*'
```

### Error
- WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
- HiveContext is deprecated in Spark 2.0.0. Please use SparkSession.builder.enableHiveSupport().getOrCreate() instead
