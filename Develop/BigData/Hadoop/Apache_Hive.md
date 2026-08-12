---
source_title: Apache Hive
categories:
- Develop
- Hadoop
- Hive
last_modified: '2023-02-10T08:44:04Z'
---
![Apache-hive.jpg](https://mwbbs.eu.org/wiki/images/e/ec/Apache-hive.jpg)

The Apache Hive ™ is a distributed, fault-tolerant data warehouse system that enables analytics at a massive scale. Hive Metastore(HMS) provides a central repository of metadata that can easily be analyzed to make informed, data driven decisions, and therefore it is a critical component of many data lake architectures. Hive is built on top of Apache Hadoop and supports storage on S3, adls, gs etc though hdfs. Hive allows users to read, write, and manage petabytes of data using SQL.

[Github-Hive](https://github.com/apache/hive)

Hive 由 Facebook 实现并开源，是基于 HDFS 的 MapReduce 计算框架，对存储在 HDFS 中的数据进行分析和管理。Hive的本质是将 SQL 语句转换为 MapReduce 任务运行，使不熟悉 MapReduce 的用户很方便地利用 HQL 处理和计算 HDFS 上的结构化的数据，适用于离线的批量数据计算。

The Hive Metastore （HMS） 是关系数据库中 Hive 表和分区的元数据的中央存储库，为 Hive、Impala、Spark 等提供对此信息的访问权限。它是 Apache Spark、Presto 等应用的基础。事实上，整个工具生态系统，无论是开源的还是其他，都是围绕 Hive Metastore 构建的。

Hive SQL 简称 HQL。hive 的执行引擎可以是 MR、Spark、tez。如果执行引擎是MapReduce的话，hive会将Hql翻译成MR进行数据的计算。

![Apache-hive-hms.jpg](https://mwbbs.eu.org/wiki/images/c/c8/Apache-hive-hms.jpg)

### Hive 特点

#### 可扩展

Hive 可以自由的扩展集群的规模，一般情况下不需要重启服务。可扩展/可伸缩意味着在 Hadoop 的集群上动态的添加设备、扩充容量。

#### 延展性

Hive 支持用户自定义函数，用户可以根据自己的需求来实现自己的函数。

#### 容错

良好的容错性，节点出现问题 SQL 仍可完成执行。

### Hive 问题

#### 事务

数据库支持事务，可读可写。而hive不支持事务，一般用于读多写少的情况，不建议改动数据，因为数据存储在 HDFS 中，而 HDFS 的文件不支持修改；

*hive 在0.14以后的版本支持事务，前提是文件格式为 orc 格式，同时必须分桶，还必须显式声明 transactional=true

*Hive provides full acid support for ORC tables out and insert only support to all other formats.

#### 延迟

hive 延迟比较大，因其底层是 MapReduce，执行效率较慢。但当数据规模较大的情况下，hive的并行计算优势就体现出来了，数据库的效率就不如 hive 了

#### 索引

hive 不支持主键或者外键，在指定列上建立索引，会产生一张索引表（物理表），里面的字段包括，索引列的值、该值对应的HDFS文件路径、该值在文件中的偏移量。
- 索引表不会自动 rebuild，如果表有数据新增或删除，那么必须手动 rebuild 索引表数据
- Hive从0.7.0版本开始加入了索引，0.8版本后增加 bitmap 索引，用于排重后，值较少的列（例如，某字段的取值只可能是几个枚举值）

### Driver

#### 解析器（SQL Parser）

将 HQL 字符串转换成抽象的语法树 AST，这一步一般都用第三方工具库完成，比如 antlr：对 AST 进行语法分析，比如表是否存在、SQL 语义是否有误。

#### 编译器（Compiler）

对HQL语句进行词法、语法、语义的编译（需要跟元数据关联），编译完成后会生成一个执行计划。hive 上就是编译成 mapreduce 的 job。

#### 优化器（Optimizer）

将执行计划进行优化，减少不必要的列、使用分区、使用索引等。优化 job。

#### 执行器（Executer）

将优化后的执行计划提交给 Hadoop 的 yarn 上执行。提交 job。

### 工作原理
1. 用户提交查询等任务给Driver
1. 驱动程序将 Hql 发送编译器，检查语法和生成查询计划
1. 编译器 Compiler 根据用户任务去 MetaStore 中获取需要的 Hive 的元数据信息
1. 编译器 Compiler 得到元数据信息，对任务进行编译，先将 HiveQL 转换为抽象语法树，然后将抽象语法树转换成查询块，将查询块转化为逻辑的查询计划，重写逻辑查询计划，将逻辑计划转化为物理的计划（MapReduce）, 最后选择最佳的策略
1. 将最终的计划提交给 Driver。到此为止，查询解析和编译完成
1. Driver 将计划 Plan 转交给 ExecutionEngine 去执行
1. 在内部，执行作业的过程是一个 MapReduce 工作。执行引擎发送作业给 JobTracker，在名称节点并把它分配作业到 TaskTracker，这是在数据节点。在这里，查询执行 MapReduce 工作。与此同时,在执行时,执行引擎可以通过 Metastore 执行元数据操作
1. 执行引擎接收来自数据节点的结果
1. 执行引擎发送这些结果值给驱动程序
1. 驱动程序将结果发送给 Hive 接口

### 其它

hive 将元数据存储在数据库中，如MySQL、derby。默认将元数据存储在 derby 数据库中，但其仅支持单线程操作。

而且当在切换目录后，重新进入 Hive 会找不到原来已经创建的数据库和表，因此一般用 MySQL 存储元数据。

hive中的元数据包括（表名、表所属的数据库名、表的拥有者、列/分区字段、表的类型（是否是外部表）、表的数据所在目录等）
