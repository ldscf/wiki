---
source_title: Doris基础
categories:
- DB
- Develop
- Doris
last_modified: '2024-01-25T01:08:21Z'
---
#### 连接
```
 # mysql -h fe_server -P query_port -uroot
 mysql -h 192.168.0.158 -P 9030 -uroot -p
```

#### 数据模型

默认副本数为3。如果 BE 节点数量小于3，则需指定副本数小于等于 BE 节点数量。

非默认副本数，需要在建表时指定。

properties ("replication_allocation" = "tag.location.default: 1")

数据修改

Duplicate 类型表数据无法修改，有主键表无法修改主键列值。

##### Aggregate

聚合模型
- Value 列会按照设置的 AggregationType 进行聚合，如：sum, max, replace 等
- AGGREGATE KEY() 指定 key，未被指定的，需要提供  AggregationType，如：`cost` BIGINT SUM DEFAULT "0" 
- 读时合并（merge on read)，因此在一些聚合查询上性能不佳
```
 create table test_a
 (
    ky   int,
    name varchar(10),
    val  int sum default "0" 
 )
 aggregate key(ky, name)
 distributed by hash(`ky`) buckets 1
 properties (
    "replication_allocation" = "tag.location.default: 1"
 )
```

##### Unique

唯一模型
- 保持 key 列的唯一，新值替换旧值
- 写时合并（merge on write）
- 可以在 be.conf 中添加配置项 disable_storage_page_cache=false，可能会优化数据导入性能
```
 create table test_u
 (
    ky   int,
    name varchar(10),
    val  int
 )
 unique key(ky, name)
 distributed by hash(ky) buckets 1
 properties (
    "replication_allocation" = "tag.location.default: 1",
    "enable_unique_key_merge_on_write" = "true"
 )
```

##### Duplicate

可重复模型
- 不对导入数据做任何操作
- 建表语句中指定的 DUPLICATE KEY，只是用来指明底层数据按照那些列进行排序。（更贴切的名称应该为 “Sorted Column”）
```
 create table test
 (
    ky   int,
    name varchar(10),
    val  int
 )
 distributed by hash(ky) buckets 1
 properties (
    "replication_allocation" = "tag.location.default: 1",
    "enable_duplicate_without_keys_by_default" = "true"
 )
```

##### 分区、分桶
- list

10 个分区，6 个桶，3 个副本
```
 create table test_p
 (
    part tinyint not null,
    ky   int,
    name varchar(10),
    val  int
 )
 duplicate key(part, ky)
 partition by list(part)
 (
    partition p_0 values in(0),
    partition p_1 values in(1),
    partition p_2 values in(2),
    partition p_3 values in(3),
    partition p_4 values in(4),
    partition p_5 values in(5),
    partition p_6 values in(6),
    partition p_7 values in(7),
    partition p_8 values in(8),
    partition p_9 values in(9)
    -- partition p_0 values in(2,4,6,8,0),
    -- partition p_1 values in(1,3,5,7,9)
 )
 distributed by hash(ky) buckets 6
 properties (
    "replication_allocation" = "tag.location.default: 3"
 )
```
- range
```
 create table test1
 (
    part tinyint not null,
    ky   int,
    name varchar(10),
    val  int
 )
 duplicate key(part, ky)
 partition by range(part)
 (
    partition p_0 VALUES less than (5),
    partition p_1 VALUES less than (10),
    partition p_9 VALUES less than maxvalue
    -- partition p_0 VALUES [(0), (5)),
    -- partition p_1 VALUES [(6), (10))
 )
 distributed by hash(ky) buckets 6
 properties (
    "replication_allocation" = "tag.location.default: 3"
 )
```

Doris 采用两级分区，第一级是 Partition，通常可以将时间作为分区键，第二级为 Bucket，通过 Hash 将数据打散至各个节点中，以此提升读取并行度并进一步提高读取吞吐。通过合理地划分区分桶，可以提高查询性能。

#### 索引

包括智能索引和二级索引两种。

##### 智能索引

在 Doris 数据写入时自动生成的，包括前缀索引和 ZoneMap 索引两类。
- 前缀稀疏索引（Sorted Index） 是建立在排序结构上的一种索引。Doris 存储在文件中的数据，是按照排序列有序存储的，Doris 会在排序数据上每 1024 行创建一个稀疏索引项。索引的 Key 即当前这 1024 行中第一行的前缀排序列的值，当用户的查询条件包含这些排序列时，可以通过前缀稀疏索引快速定位到起始行。
- ZoneMap 索引是建立在 Segment 和 Page 级别的索引。对于 Page 中的每一列，都会记录在这个 Page 中的最大值和最小值，同样，在 Segment 级别也会对每一列的最大值和最小值进行记录。这样当进行等值或范围查询时，可以通过 MinMax 索引快速过滤掉不需要读取的行。

##### 二级索引

手动创建的索引，包括 Bloom Filter 索引、Bitmap 索引，以及 2.0 版本新增的 Inverted 倒排索引和 NGram Bloom Filter 索引。
