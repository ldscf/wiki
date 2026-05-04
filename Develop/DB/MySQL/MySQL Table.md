---
source_title: MySQL Table
categories:
- DB
- Develop
- MySQL
last_modified: '2023-11-20T06:34:47Z'
---
### 普通表
```
 create table test_seq (
    ky               int auto_increment,
    val              varchar(50),
    ver              json,
    primary key(ky)
 )
 ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE= utf8mb4_0900_ai_ci
 auto_increment = 10000000
```
```
 CREATE INDEX i_test_seq_val ON test_seq(val);
```

#### 排序规则
- utf8mb4_0900_ai_ci
- utf8mb4_unicode_ci
- utf8mb4_general_ci

当设置表的默认字符集为 utf8mb4 字符集但未明确指定排序规则时：
- 在 MySQL 5.7 版本中，默认排序规则为 utf8mb4_general_ci
- 在 MySQL 8.0 版本中，默认排序规则为 utf8mb4_0900_ai_ci

utf8mb4_general_ci 一个遗留的校对规则，不支持扩展，它仅能够在字符之间进行逐个比较，这意味着 utf8mb4_general_ci 校对规则进行的比较和排序的时候速度很快。但没有实现 Unicode 排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致。

utf8mb4_unicode_ci 是基于标准的 Unicode 来排序和比较，能够在各种语言之间精确排序。如对于德语和法语。

将 MySQL 8.0 版本的表导入到 MySQL 5.7 或以下版本时，可能会存在字符集无法识别的问题
- [Err] 1273 - Unknown collation: 'utf8mb4_0900_ai_ci'

#### 自增主键
- auto_increment 为自增主键（该列必须设为主键），一般使用 int（小于 42 亿）。不支持 decimal(10) 这种字段格式
- auto_increment 也可以使用 double 这种浮点数格式，自增 +1，但可以指定插入 10.1 这种（表中最大自增为 10.1 时，下一个系统自增是 11）
```
 insert into test_seq(val, ver) values
  ('mysql', '{"v1":"10", "v2":"11"}'),
  ('db2', '{"v1":"8", "v3":"9"}')
```

#### JSON
```
 SELECT   ky,
          val,
          ver ->> '$.v1'   v1,
          ver ->> '$.v2'   v2,
          ver ->> '$.v3'   v3
 FROM     test_seq
```
- "->" 带引号，"->>"无引号
- 无字段则值返回空

### 分区表
```
 alter table test_seq partition by hash(ky) partitions 64;
```

最多支持 1024 个分区，同时总数量受库参数打开文件数量限制。

### 临时表

CREATE TEMPORARY TABLE
- 表结构、数据均存放在内存中
- 临时表在连接使用期间存在，断开时，MySQL 将自动删除表并释放所用的空间
- 不能用 rename 来重命名一个临时表，可以用 alter table 代替

### 数据库存储引擎
- InnoDB 支持事务，MyISAM 不支持。对于 InnoDB 每一条 SQL 语句都默认封装成事务进行提交，这样就会影响速度，优化速度的方式是将多条 SQL 语句放在 begin 和 commit 之间，组成一个事务。InnoDB 支持外键，而 MyISAM 不支持
- 将查询要求比较多的表选择 MyISAM 存储
- 用于查询的表，也可以考虑选择 MEMORY 存储引擎

索引
- InnoDB 中 B+ 树的数据结构中存储的都是实际的数据，这种索引有被称为聚集索引
- MyISAM 中 B+ 树的数据结构存储的内容是实际数据的地址值，它的索引和实际数据是分开的，只不过使用索引指向了实际数据。这种索引的模式被称为非聚集索引

#### InnoDB

读写数据时是硬盘寻址，要减少这个硬盘寻址读取的次数，一般是按页读取数据，并可使用内存缓存。InnoDB 将一个表的数据划分成了若干页（pages），这些页通过 B-Tree 索引联系起来。每一页大小默认为 16384 Bytes （innodb_page_size）。这个 B-Tree 索引就是聚簇索引（Clustered Index），如果表有主键，那么主键索引就是这个聚簇索引。

InnoDB 对于变长字段，一般倾向于将他们存储在其他地方。至于怎么存储，这个还和 InnoDB 行格式（InnoDB Row Format）有关。行格式一共有四种：Compact、Redundant、Dynamic 和 Compressed。
- COMPACT 和更老一点的 REDUNDANT 行格式中，对于占用空间较大的列，在该记录行存储该列的一部分数据（前 768 个字节），把剩余的数据存储在溢出页，用 20 个字节存储存储溢出数据的地址。
- DYNAMIC 和 COMPRESSED 行格式把溢出列的所有数据都存储在溢出页当中，用 20 个字节去记录这些溢出数据的地址
- COMPRESSED 比 DYNAMIC会多做一步：采用压缩算法对页面进行压缩

#### MyISAM

#### 内存表

ENGINE=**MEMORY**

表结构保存在磁盘上，数据存放在内存中（重启会只有结构）。MEMORY 快大概 20％。

内存表的数据存放在内存中，而内部临时表（如查询时产生的）一般放在内存中，但当内部临时表较大时，会自动转化为磁盘存储。内存表不会自动转换。
- 数据使用 hash 的方式存储，故只支持 = 或 <>
- max_heap_table_size 默认为 16777216，单张表行数超过则报错
- 对于 varchar 等变长类型，使用最大的长度
- 可以有非唯一键
- 不能包含 BLOB 或者 TEXT
- 支持 AUTO_INCREMENT
- 不支持事务，表锁
- 可能会插入延迟，使读取优先
