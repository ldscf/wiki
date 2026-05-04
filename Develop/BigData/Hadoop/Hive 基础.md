---
source_title: Hive 基础
categories:
- Develop
- Hadoop
- Hive
last_modified: '2024-12-31T02:13:33Z'
---

### 参数设置

优先级: 配置文件 < 命令行参数 < 终端里输入命令

注意某些系统级的参数, 例如 log4j 的设定必须用前两种方式设定, 因为那些参数的读取在会话建立以前已经完成了。
- 配置文件方式 hive-default.xml 和 hive-site.xml
- 命令行参数方式 启动 Hive 时，hive -hiveconf mapred.reduce.tasks=10
- 终端里输入命令
 ```

# 语法
set       查看所有配置
set xxx   查看 xxx 参数的值
set xxx=1 设置 xxx 参数的值

## Sample

# 设置
SET mapreduce.job.counters.max = 200;

# 查看
SET mapreduce.job.counters.max;
P.S. 上面变量可以显示变更，但并未生效，需要在 mapred-site.xml 配置并重启。

# 改变引擎，当前运行环境中生效
set hive.execution.engine = mr;
set hive.execution.engine = tez;

# 变量在 SQL 中使用
set tbl = emp;
SELECT * FROM ${hiveconf:tbl};
```

### 命令行

*
```
 beeline -u jdbc:hive2://localhost:10000/default -n hdfs
 #
 beeline -.OR.- hive
```


```
 # 执行 SQL
 -e "select * from emp;select 5+3"
 # 执行文件
 -f sql/emp.sql
```

 
```
 ## 关闭日志
 # 命令行参数
 --hiveconf hive.server2.logging.operation.level=NONE
 # 或者在 $HIVE_HOME/conf/hive-site.xml
 hive.server2.logging.operation.enabled: [true|false]
 hive.server2.logging.operation.level:
  NONE：忽略任何日志记录
  EXECUTION：记录任务完成情况
  PERFORMANCE: 执行+性能日志
  VERBOSE：所有日志
 hive.server2.logging.operation.log.location:
  /opt/hive/log*
```

### 分割符

Hive中默认的分割符为：
* 列：^A
* 行：\n

在数据文件中显示为："1000^AHello, World!\n"，数据中如果包含："\001"，"\n"等分隔符，需要提前处理掉。

如果需要自定义分隔符，需要设置：
 ```
1. 分隔符为逗号，字符串中有特殊字符用双引号引起来
create table test_csv(
  ky int,
  val string,
  ct string,
  memo string
)
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.serde2.OpenCSVSerde' 
WITH SERDEPROPERTIES ( 
  'quoteChar'='"', 
  'separatorChar'=',', 
  'serialization.encoding'='UTF-8')
stored as textfile;
```

### 数据类型

#### 基本数据类型

| 基础数据类型 | 字节数 | Memo |
|:---|:---|:---|
| TINYINT | 1 | -128~127 |
| SMALINT | 2 | -32768~32767 |
| INT | 4 | 默认，-2147483648~2147483647 |
| BIGINT | 8 | 18位，-9.2234e18~9.2234e18 |
| BOOLEAN | 1 | true/false |
| FLOAT | 4 | 4字节单精度浮点数 |
| DOUBLE | 8 | 8字节单精度浮点数 |
| DEICIMAL | 任意精度的带符号小数 | 1.23 |
| CHAR | 固定长度字符串 |
| VARCHAR | 变长 |
| STRING | 变长 | 2G |
| DATE | 日期 | 2000-12-31 |
| TIMESTAMP | 时间戳，毫秒值精度 | 122327493795 |
| INTERVAL | 时间频率间隔 |

#### 集合数据类型

| 集合数据类型 | Memo |
|:---|:---|
| STRUCT | struct |
| MAP | map |
| ARRAY | array |

### 索引、分区与分桶

#### 索引

Hive从0.7.0版本开始加入了索引，0.8版本后增加 bitmap 索引。索引表不会自动 rebuild，如果表有数据新增或删除，那么必须手动 rebuild 索引表数据

#### 分区

表分区是指将数据按照物理分层的方式进行区分开，加快查询的速度，同时也起到数据快照的作用。可以指定单个字段也可以指定多个字段；
* 静态分区是在创建表时手动指定
* 动态分区是通过数据来进行判断，只有在 SQL 执行时才能确定。动态分区不能使用 load 加载数据，需要使用 insert into

默认创建的分区是静态分区，如果要指定动态分区需要配置系统参数。

#### 桶

当单个分区或者表中的数据量越来越大，当分区不能更细粒的划分数据时，采用分桶技术将数据更细粒度的划分和管理。

分桶关键字：BUCKET

指定分桶的字段：clustered by (uid)

#### 区别
* 分区使用的是表外字段，分桶使用的是表内字段
* 分桶是更细粒度的划分、管理数据，更多用来做数据抽样、JOIN操作
* 分区是粗粒度的将数据隔离，分桶是更加细粒度的将数据隔离

### Data

#### Load
```
 LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1,partcol2=val2 ...)]
```

#### Load CSV

CSV 导入模式是先导入成 textfile, 之后再从临时表 insert 成 parquet。

注意：若 Load CSV 无报错，但导入皆为 NULL，此种情况一般为格式问题（如导入 iceberg 格式表时，需要先导入 LazySimpleSerDe/OpenCSVSerde 格式表）。
* 有 LOCAL 表示从本地文件系统加载（文件会被拷贝到 HDFS 中）
* 无 LOCAL 表示从 HDFS 中加载数据（注意：文件直接被移动到 Hive 相应库下，而不是拷贝）
* OVERWRITE 表示是否覆盖表中数据（或指定分区的数据）（没有 OVERWRITE 则 APPEND）
* 若加载同样文件名的文件，会被自动重命名
 ```
1. test_csv.csv
ky,val,ct,memo
1001,Hello,2021-1-1,Test
0002,Hi,2021-1-2 1:00,test1
1002,"Hello, World!",2021-1-3 10:00,test2
1003,"Hi, ada""'
return",2021-1-4 15:00,Test message'.
1004,"Hello, BI.",2021-1-10,
load data local inpath '/u01/data/test_csv.csv' overwrite into table test.test_csv;
Hive Load Data 时，并不去掉第一行标题（导入后格式与源文件相同）。一般提前处理，或者使用下面语句：
alter table test.test_csv set TBLPROPERTIES('skip.header.line.count'='1');
```

#### Load Hive

另一种处理方式：将 CSV 格式转换为 Hive 格式
```
 cat bank.csv | sed "s/,/$PA/" > bank.hive    # PA=OX001
 load data local inpath '/u01/data/bank.hive' ~~overwrite~~ into table test.bank_data;
```

#### Insert
```
 hive> create table test ( key string, val string);
 hive> insert into test.test values ('1000', 'Hello World!');
 hive> insert into test.test values ('1010', 'Hello Hive!');
```

插入操作，每条语句均会产生一个文件：000000_0, 000000_0_copy_1

### 注意

#### Select
* order by 中的字段, 必须在 select 中出现
* 子查询表必须有别名

#### Set
```
 # 想用下列方法取执行时间是错误的
 SET tb = unix_timestamp();
 ...
 SET te = unix_timestamp();
 SELECT (${hiveconf:te} - ${hiveconf:tb}) AS execution_time;
 ## 原因：
 执行 SET tb = 时，没有想像中的保存当时的系统时间，而保存的是 unix_timestamp()。这样在 select 两个值相减时，结果为零。
 ## 解决：无
```
