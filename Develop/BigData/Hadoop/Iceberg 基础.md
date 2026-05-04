---
source_title: Iceberg 基础
categories:
- Develop
- Hadoop
- Hive
- Iceberg
last_modified: '2024-12-31T01:53:03Z'
---
Iceberg 表版本（V1和V2）：
1. V1 定义了如何使用不可变类型的文件（Parquet、ORC、AVRO）来管理大型分析型的表，包括元数据文件、属性、数据类型、表的模式，分区信息，以及如何写入与读取
1. V2 在 V1 的基础上增加了如何通过这些类型的表实现行级别的更新与删除功能。其最主要的改变是引入了 delete file 记录需要删除的行数据，这样可以在不重写原有（数据）文件的前提下，实现行数据的更新与删除

一般来说：
1. V1 表只支持增量数据插入，适合做纯增量写入场景，如埋点数据表
1. V2 表支持行级更新，适合做状态变化的更新，如用户表、订单表
 ```
TBLPROPERTIES (
  'format-version'='2',
  'parquet.compression'='zstd',
)

## 同样数据量（四千万），使用 zstd 压缩的 iceberg 表大幅减少空间占用

# hdfs dfs -du -h /user/hive/warehouse
 2.8 G    8.3 G    /user/hive/warehouse/test1
 132.3 M  396.8 M  /user/hive/warehouse/test_ice

## 插入 2.4 亿条数据后

# hdfs dfs -du -h hdfs://hdfs00:9000/user/hive/warehouse/tf2_proc/
 1.2 G    3.5 G    hdfs://hdfs00:9000/user/hive/warehouse/tf2_proc/data
 153.1 K  459.3 K  hdfs://hdfs00:9000/user/hive/warehouse/tf2_proc/metadata
 5.8 K    17.3 K   hdfs://hdfs00:9000/user/hive/warehouse/tf2_proc/stats
 0        0        hdfs://hdfs00:9000/user/hive/warehouse/tf2_proc/temp
```

以下测试环境，采用 Centos 7，四台 4 线程 Intel(R) Core(TM) i5-8400 CPU @ 2.80GHz，8G RAM 虚机完成。

### hive -> iceberg

#### hive
 ```

# 分隔符为空格，字符串中有空格用双引号引起来
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

# hdfs dfs -put test1.csv /tmp/data/
LOAD DATA INPATH '/tmp/data/test1.csv' OVERWRITE INTO TABLE test1;
select col4, count(*) cs from test1 group by col4 limit 10; 
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED     30         30        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 99.62 s    
---
INFO  : Completed executing command(queryId=hdfs_20241224111257_4f4363b0-d489-4bac-99d8-86451fa0a45c); Time taken: 99.725 seconds
+----------------+-----------+

|      col4      |    cs     |
+----------------+-----------+

| xeron x5 3708  | 40075712  |
+----------------+-----------+
```

#### iceberg
 ```

# 分区字段不能出现在建表字段中
CREATE TABLE test_ice (
  col1 STRING,
  col2 INT,
  col4 STRING,
  col5 STRING
)
PARTITIONED BY (col3 STRING)
STORED by iceberg;
insert into test_ice(col1, col2, col3, col4, col5) select col1, col2, col3, col4, col5 from test1;
select col4, count(*) cs from test_ice group by col4 limit 10; 
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED      4          4        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 22.68 s    
---
INFO  : Completed executing command(queryId=hdfs_20241224111143_d436171c-f9fe-4e9a-87e1-0e2ea9b48b1b); Time taken: 26.945 seconds
```

#### 运行时
 ```

# vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
24  2      0 4735832   1816 6360404    0    0     3    25    4   23  2  1 97  0  0
31  0      0 4675836   1816 6360596    0    0     0    41 2591 3733 85 15  0  0  0
44  0      0 4615600   1816 6360656    0    0     0    80 2943 5270 88 11  1  0  0
65  0      0 4531204   1816 6360548    0    0     0    24 3148 4973 86 14  1  0  0
38  0      0 4477960   1816 6360580    0    0     0    28 2751 6194 88 12  0  0  0
...
 1  3      0 3117460   1708 7576584    0    0     0     0 2368 3685  1  1 49 49  0
 0  3      0 3117144   1708 7576596    0    0     0 16384 2550 3815  1  1 50 49  0
...
 0  0      0 3062816   1708 8845520    0    0     0   108 4263 7792  3  1 95  1  0
 0  0      0 3062804   1708 8845684    0    0     0   100 4112 8209  2  1 97  1  0

# top
Tasks: 259 total,   2 running, 257 sleeping,   0 stopped,   0 zombie
%Cpu(s): 92.3 us,  6.7 sy,  0.0 ni,  0.1 id,  0.0 wa,  0.0 hi,  0.9 si,  0.0 st
KiB Mem : 16266524 total,   273132 free,  9388948 used,  6604444 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  6373620 avail Mem 
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                        
23006 hdfs      20   0 2536576 551248  25184 S  57.8  3.4   0:28.10 java
22892 hdfs      20   0 2507524 491888  25188 S  55.8  3.0   0:28.05 java
23020 hdfs      20   0 2534372 636328  25168 S  55.1  3.9   0:23.80 java
22887 hdfs      20   0 2532756 646060  25176 S  46.8  4.0   0:27.88 java
22895 hdfs      20   0 2537776 662796  25184 S  40.9  4.1   0:30.90 java
22959 hdfs      20   0 2506228 502380  25180 S  40.5  3.1   0:28.43 java
22897 hdfs      20   0 2507996 486764  25188 S  39.5  3.0   0:28.39 java
23004 hdfs      20   0 2504672 652876  25176 S  35.5  4.0   0:30.46 java
```

### Iceberg 性能

#### 存储

| *Table* | *Rows* | *Secs* | *HDFS* | *Size* | *gz* |
|:---|:---|:---|:---|:---|:---|
| + |
| *OpenCSVSerde* | *6* | *48.47* | *124* |
| *iceberg* | *6* | *6.62* | *29.3 K* |
| *OpenCSVSerde* | *40075712* | *462.89* | *2.8 G* | *2.8 G* | *478.6 M* |
| *OpenCSVSerde* | *2504732* | *9.56* | *176.8 M* |
| *iceberg* | *40075712* | *27.44* | *132.3 M* |
| *iceberg* | *240454272* | *0.13* | *1.2 G* |

#### SQL

| *Source* | *Target* | *OP* | *Action* | *Res* | *Secs* | *Rows* |
|:---|:---|:---|:---|:---|:---|:---|
| + |
| *iceberg* | *iceberg* | *insert* | *limit 100000000* | *0* | *2218.38* | *40,075,712* |
| *iceberg* | *iceberg* | *insert* | *0* | *104.96* | *40,075,712* |
| *iceberg* | *iceberg* | *select* | *count* | *1* | *42.51* | *240,454,272* |
| *iceberg* | *iceberg* | *select* | *group, count* | *100* | *159.76* | *240,454,272* |
 ```
CREATE TABLE tf2_proc (
  col1 STRING,
  col2 INT,
  col3 STRING,
  col4 STRING,
  col5 STRING
)
PARTITIONED BY (did int)
STORED by iceberg;

# 虽然写了 limit，但还是全量插入了（40,075,712 rows）。而且时间比不写要长很多，执行计划也不一致。参考下列。
insert into tf2_proc select substr(col1, 1, length(col1) - 1) as col1, col2, col3, 'Xeron x7 5703' col4, 'INTER 128G' col5, '20241201' did from test_ice limit 100000000;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED
---
Map 1 .......... container     SUCCEEDED      4          4        0        0       0       0
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       1       0
Reducer 3 ...... container     SUCCEEDED     63         63        0        0       1       0
Reducer 4 ...... container     SUCCEEDED      1          1        0        0       0       0
---
VERTICES: 04/04  [==========================>>] 100%  ELAPSED TIME: 2218.38 s
---
INFO  : Completed executing command(queryId=hdfs_20241225095952_f073fd47-a7ea-4189-8fc2-e2c0476bfbc3); Time taken: 2221.005 seconds
40,075,712 rows affected (2221.748 seconds)
insert into tf2_proc select substr(col1, 1, length(col1) - 1) as col1, col2, col3, 'Xeron x5 2523' col4, 'INTER 64G' col5, '20241202' did from test_ice;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED
---
Map 1 .......... container     SUCCEEDED      4          4        0        0       0       0
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       0       0
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 99.64 s
---
NFO  : Completed executing command(queryId=hdfs_20241225111354_e1d0e0e2-5ff6-4e18-bf07-57c2030bb746); Time taken: 104.964 seconds
40,075,712 rows affected (105.205 seconds)
select count(*) cs from tf2_proc;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 42.18 s    
---
INFO  : Completed executing command(queryId=hdfs_20241225135649_5b485208-589c-4d3b-a1d9-c855d91cc698); Time taken: 42.512 seconds
+------------+

|     cs     |
+------------+

| 240454272  |
+------------+
1 row selected (42.919 seconds)
select did, count(*) cs from tf2_proc group by did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 112.83 s   
---
INFO  : Completed executing command(queryId=hdfs_20241225135135_1d11a0a1-6512-454b-983b-8b7ee6821ddb); Time taken: 159.766 seconds
+-----------+-----------+

|    did    |    cs     |
+-----------+-----------+

| 20241201  | 40075712  |
| 20241202  | 80151424  |
| 20241203  | 40075712  |
| 20241204  | 40075712  |
| 20241205  | 40075712  |
+-----------+-----------+
5 rows selected (161.266 seconds)
select did, count(distinct col3) col3s, count(distinct col4) col4s, count(distinct col5) col5s, count(*) cs from tf2_proc group by did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      3          3        0        0       0       0  
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 35.33 s    
---
INFO  : Completed executing command(queryId=hdfs_20241225141026_24609e0f-3a6a-42a6-9665-b8699cd05408); Time taken: 35.42 seconds
+-----------+--------+--------+--------+-----------+

|    did    | col3s  | col4s  | col5s  |    cs     |
+-----------+--------+--------+--------+-----------+

| 20241201  | 900    | 1      | 1      | 40075712  |
| 20241202  | 900    | 1      | 1      | 80151424  |
| 20241203  | 900    | 1      | 1      | 40075712  |
| 20241204  | 900    | 1      | 1      | 40075712  |
| 20241205  | 900    | 1      | 1      | 40075712  |
+-----------+--------+--------+--------+-----------+
5 rows selected (35.558 seconds)
select did, count(distinct col3) col3s, count(distinct col4) col4s, count(distinct col5) col5s, count(*) cs from tf2_proc where col2 = 89846593 or col1 = '101.33021.12/122128339' group by did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED
---
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       0       0
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 42.57 s
---
INFO  : Completed executing command(queryId=hdfs_20241225133024_5d6444f7-bfa1-4947-9bc0-be8e01e99ab6); Time taken: 45.627 seconds
+-----------+--------+--------+--------+-----+

|    did    | col3s  | col4s  | col5s  | cs  |
+-----------+--------+--------+--------+-----+

| 20241201  | 1      | 1      | 1      | 32  |
| 20241202  | 1      | 1      | 1      | 64  |
| 20241203  | 1      | 1      | 1      | 32  |
| 20241204  | 1      | 1      | 1      | 32  |
| 20241205  | 1      | 1      | 1      | 32  |
+-----------+--------+--------+--------+-----+
```

### SQL 优化

下列语句较长时间不出结果，但分开每一个单独查询可以很快出结果。故改写为多个查询结果合并。
 ```
select did, count(distinct col1) col1s, count(distinct col2) col2s, count(distinct col3) col3s, count(distinct col4) col4s, count(distinct col5) col5s, count(*) cs from tf2_proc group by did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .........  container        KILLED     10          9        0        1       4       5  
Reducer 2        container        KILLED    418          0        0      418       0       7  
---
VERTICES: 00/02  [>>--------------------------] 2%    ELAPSED TIME: 2223.73 s  
---
INFO  : Executing command(queryId=hdfs_20241225133257_104ae9e0-f729-46a6-9e31-d2a1b5f9d68d) has been interrupted after 2223.844 seconds
Error: Query was cancelled. (state=01000,code=0)
select did, count(distinct col1) col1s from tf2_proc group by did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED     52         52        0        0       0       0  
Reducer 3 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 03/03  [==========================>>] 100%  ELAPSED TIME: 116.92 s   
---
INFO  : Completed executing command(queryId=hdfs_20241225142434_21223613-d748-4d83-9b52-22666186d34b); Time taken: 116.998 seconds
+-----------+----------+

|    did    |  col1s   |
+-----------+----------+

| 20241201  | 2504732  |
| 20241202  | 2504732  |
| 20241203  | 2504732  |
| 20241204  | 2504732  |
| 20241205  | 2504732  |
+-----------+----------+
5 rows selected (117.138 seconds)
select did, count(distinct col2) col2s from tf2_proc group by did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      2          2        0        0       0       0  
Reducer 3 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 03/03  [==========================>>] 100%  ELAPSED TIME: 46.99 s    
---
INFO  : Completed executing command(queryId=hdfs_20241225143204_75c89b17-8ae1-4a39-9233-39dc0fed8c18); Time taken: 47.069 seconds
+-----------+----------+

|    did    |  col2s   |
+-----------+----------+

| 20241201  | 2470394  |
| 20241202  | 2470394  |
| 20241203  | 2470394  |
| 20241204  | 2470394  |
| 20241205  | 2470394  |
+-----------+----------+
5 rows selected (47.205 seconds)

# 多个虚拟表查询后合并
WITH col1_distinct AS (
    SELECT did, COUNT(DISTINCT col1) AS col1s FROM tf2_proc GROUP BY did
),
col2_distinct AS (
    SELECT did, COUNT(DISTINCT col2) AS col2s FROM tf2_proc GROUP BY did
),
col3_distinct AS (
    SELECT did, COUNT(DISTINCT col3) AS col3s FROM tf2_proc GROUP BY did
),
col4_distinct AS (
    SELECT did, COUNT(DISTINCT col4) AS col4s FROM tf2_proc GROUP BY did
),
col5_distinct AS (
    SELECT did, COUNT(DISTINCT col5) AS col5s FROM tf2_proc GROUP BY did
),
count_total AS (
    SELECT did, COUNT(*) AS cs FROM tf2_proc GROUP BY did
)
SELECT 
    c1.did,
    c1.col1s,
    c2.col2s,
    c3.col3s,
    c4.col4s,
    c5.col5s,
    ct.cs
FROM col1_distinct c1
JOIN col2_distinct c2 ON c1.did = c2.did
JOIN col3_distinct c3 ON c1.did = c3.did
JOIN col4_distinct c4 ON c1.did = c4.did
JOIN col5_distinct c5 ON c1.did = c5.did
JOIN count_total ct ON c1.did = ct.did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 4 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 5 ...... container     SUCCEEDED     52         52        0        0       0       0  
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      2          2        0        0       0       0  
Reducer 3 ...... container     SUCCEEDED      1          1        0        0       0       0  
Map 7 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 8 ...... container     SUCCEEDED      1          1        0        0       0       0  
Map 9 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 10 ..... container     SUCCEEDED      1          1        0        0       0       0  
Map 11 ......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 12 ..... container     SUCCEEDED      1          1        0        0       0       0  
Map 13 ......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 14 ..... container     SUCCEEDED      1          1        0        0       0       0  
Reducer 6 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 14/14  [==========================>>] 100%  ELAPSED TIME: 482.33 s   
---
ERROR : Status: Failed
ERROR : Counters limit exceeded: Too many counters: 121 max=120
Error: Error while compiling statement: FAILED: Execution Error, return code 2 from org.apache.hadoop.hive.ql.exec.tez.TezTask. Counters limit exceeded: Too many counters: 121 max=120 (state=08S01,code=2)

## 虽然采用了 Error 中提到的方法，两个地方都设置了变量值=1000，但依然报同样错误。
.-后续-.

## 上面提到修改后还是报错，原因是只修改了主节点的 mapred-site.xml（这是个很常见的错误），将所有节点同步修改并重启后，运行结果正常，如下：
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 4 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 5 ...... container     SUCCEEDED     52         52        0        0       0       0  
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      2          2        0        0       0       0  
Reducer 3 ...... container     SUCCEEDED      1          1        0        0       0       0  
Map 7 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 8 ...... container     SUCCEEDED      1          1        0        0       0       0  
Map 9 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 10 ..... container     SUCCEEDED      1          1        0        0       0       0  
Map 11 ......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 12 ..... container     SUCCEEDED      1          1        0        0       0       0  
Map 13 ......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 14 ..... container     SUCCEEDED      1          1        0        0       0       0  
Reducer 6 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 14/14  [==========================>>] 100%  ELAPSED TIME: 241.01 s   
---
INFO  : Completed executing command(queryId=hdfs_20241226100618_68fb157b-758f-46a0-bff7-8264bcdfee8a); Time taken: 265.937 seconds
+-----------+-----------+-----------+-----------+-----------+-----------+-----------+

|  c1.did   | c1.col1s  | c2.col2s  | c3.col3s  | c4.col4s  | c5.col5s  |   ct.cs   |
+-----------+-----------+-----------+-----------+-----------+-----------+-----------+

| 20241201  | 2504732   | 2470394   | 900       | 1         | 1         | 40075712  |
| 20241202  | 2504732   | 2470394   | 900       | 1         | 1         | 80151424  |
| 20241203  | 2504732   | 2470394   | 900       | 1         | 1         | 40075712  |
| 20241204  | 2504732   | 2470394   | 900       | 1         | 1         | 40075712  |
| 20241205  | 2504732   | 2470394   | 900       | 1         | 1         | 40075712  |
+-----------+-----------+-----------+-----------+-----------+-----------+-----------+
5 rows selected (274 seconds)

# 或者使用采样
（默认误差在 2%-5% 之间）
SELECT   did,
         APPROX_DISTINCT(col1) AS col1s,
         APPROX_DISTINCT(col2) AS col2s,
         APPROX_DISTINCT(col3) AS col3s,
         APPROX_DISTINCT(col4) AS col4s,
         APPROX_DISTINCT(col5) AS col5s,
         COUNT(*) AS cs
FROM     tf2_proc
GROUP BY did;
---
        VERTICES      MODE        STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED  
---
Map 1 .......... container     SUCCEEDED     10         10        0        0       0       0  
Reducer 2 ...... container     SUCCEEDED      1          1        0        0       0       0  
---
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 198.51 s   
---
INFO  : Completed executing command(queryId=hdfs_20241225154353_d6cc5857-5759-4d77-aa85-a995b6126efb); Time taken: 201.875 seconds
+-----------+----------+----------+--------+--------+--------+-----------+

|    did    |  col1s   |  col2s   | col3s  | col4s  | col5s  |    cs     |
+-----------+----------+----------+--------+--------+--------+-----------+

| 20241201  | 2525517  | 2500565  | 898    | 1      | 1      | 40075712  |
| 20241202  | 2525517  | 2500565  | 898    | 1      | 1      | 80151424  |
| 20241203  | 2525517  | 2500565  | 898    | 1      | 1      | 40075712  |
| 20241204  | 2525517  | 2500565  | 898    | 1      | 1      | 40075712  |
| 20241205  | 2525517  | 2500565  | 898    | 1      | 1      | 40075712  |
+-----------+----------+----------+--------+--------+--------+-----------+
5 rows selected (202.335 seconds)
```

### Error

#### Too many counters

ERROR : Counters limit exceeded: Too many counters: 121 max=120
1. mapreduce

Hive 在处理查询任务时生成了过多的计数器（counters），超过了默认限制 mapreduce.job.counters.max 的最大值 120。这个问题通常出现在数据量巨大或查询涉及复杂计算时，比如使用多个 COUNT(DISTINCT) 或复杂的子查询。
```
 SET mapreduce.job.counters.max=1000;
```

See Also: [mapred-site.xml](https://mwbbs.eu.org/wiki/index.php/Tez#mapred-site.xml)
2. Tez
```
 set tez.runtime.task.counters.limit=1000;
 # 此项只是参考：实际上只修改了第一项里面的 mapred-site.xml 并重启，运行结果正常。
```
