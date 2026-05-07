---
source_title: Tez
categories:
- Develop
- Hadoop
- Hive
last_modified: '2024-12-25T07:18:36Z'
---
Tez 是支持 DAG 作业的开源计算框架，它可以将多个有依赖的作业转换为一个作业，从而大幅提升 DAG 作业的性能。

Tez 源于 MapReduce 框架，核心思想是将 Map 和 Reduce 两个操作进一步拆分：
1. Map: Input、Processor、Sort、Merge、Output
1. Reduce: Input、Shuffle、Sort、Merge、Processor、Output

优点
1. 避免中间数据写回 HDFS，减小任务执行时间
1. vertex management 模块使 runtime 动态修改执行计划变成可能
1. input/processor/output 编程模型，大大提高了任务模型的灵活性
1. 提供 container 复用机制与 Tez Session，减少资源消耗

缺点
1. Tez 与 Hive 捆绑，在其他领域应用较少
1. 社区不活跃
1. 完全基于内存，如果数据量特别大(注：应该指的是结果集的数据量)，容易 OOM

一般用于快速出结果，结果集小的场景，如汇总查询等。

### Inst

Hadoop 3.4.1/Hive 4.0.1/Tez 0.10.4

#### .bashrc
 ```

# Tez
export TEZ_HOME=/opt/tez
export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$TEZ_HOME/*:$TEZ_HOME/lib/*
```

#### tez-site.xml
 ```

# $TEZ_HOME/conf/tez-site.xml
    
        tez.lib.uris
        hdfs://192.168.0.249:9000/user/tez/tez.tar.gz
    
```

#### mapred-site.xml
 ```

# $HADOOP_HOME/etc/hadoop/mapred-site.xml

# $HADOOP_HOME/sbin/start-yarn.sh
  
    mapreduce.framework.name
    yarn-tez
  
  
    mapreduce.job.counters.max
    1000
  
```

### Hive

#### engine

(Hadoop 3.4.1/Hive 4.0.1 & Iceberg 1.4.3/Tez 0.10.4) 使用 MR 引擎，向 iceberg 表插入数据，不报错但无数据。换用 Tez 正常。但上述版本，SQL 查询正常，Load Data to Hive 正常，Insert 数据 MR 引擎 OK，Tez 报错。
 ```
set hive.execution.engine = tez;
set hive.execution.engine = mr;
```
```
 # $HIVE_HOME/conf/hive-site.xml
```
  
    hive.execution.engine

    tez
  

#### Sample
 ```
beeline -u jdbc:hive2://192.168.0.249:10000/ -n hdfs

## CSV 格式：空格分隔，含特殊字符的字符串用双引号

#   ID-1000012	 "77132693" "IBM x688" "xeron x5 3708" "INTER-64G"

## Create
CREATE TABLE test1 (
  col1 STRING,
  col2 INT,
  col3 STRING,
  col4 STRING,
  col5 STRING
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
WITH SERDEPROPERTIES (
    "separatorChar" = " ",
    "quoteChar" = "\"",
    "serialization.encoding"="UTF-8"
)
STORED AS TEXTFILE;

## Load
LOAD DATA INPATH '/tmp/data/test1.csv' OVERWRITE INTO TABLE test1;

## Query
set hive.execution.engine = mr;
select col4, count(*) cs from test1 group by col4 limit 10;
+----------------+-----------+

|      col4      |    cs     |
+----------------+-----------+

| xeron x5 3708  | 40075712  |
+----------------+-----------+
1 row selected (102.087 seconds)
set hive.execution.engine = tez;
select col4, count(*) cs from test1 group by col4 limit 10;
+----------------+-----------+

|      col4      |    cs     |
+----------------+-----------+

| xeron x5 3708  | 40075712  |
+----------------+-----------+
1 row selected (135.789 seconds)
vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
13  8      0 193184   2096 6090004    0    0     2    27    3   20  2  1 97  0  0
19  1      0 170348   2096 6107060    0    0 16804    70 10260 13559 55  9  2 33  0
 4  5      0 411680   2096 6080720    0    0 21696   970 9404 12037 57 13 16 15  0
18  4      0 388096   2096 6109332    0    0 28224    86 5808 7769 36 12  9 43  0
 0  1      0 377496   2096 6115656    0    0  6368   177 5455 8271 25  9 46 21  0
```
