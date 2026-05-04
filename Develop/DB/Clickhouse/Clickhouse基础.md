---
source_title: Clickhouse基础
categories:
- Clickhouse
- DB
- Develop
last_modified: '2023-05-27T14:46:02Z'
---
#### 物化视图

物化视图(Materialized View) 与普通视图不同的地方在于它是一个查询结果的数据库对象(持久化存储)，是数据库中的预计算逻辑+显式缓存，典型的空间换时间，在查询多表关联 SQL 时复用结果，从而显著提升查询的性能。（普通视图(View) 指的是通过一张或多张表查询出来的逻辑表，本身只是一段 SQL 的封装并不存储数据。）
- 物化视图会随原表插入数据而更改。如果是多表，则使用左表原则——左表插入后更新
- 原表中执行更新和删除操作后，物化视图并没有被更改，只有原表执行插入操作才会使物化视图发生更改（插入触发原则）
- 创建时，若有 POPULATE 则在创建视图的过程会将源表已经存在的数据一并导入，若无POPULATE 则物化视图在创建之后没有数据。（ClickHouse 官方并不推荐使用populated，因为在创建视图过程中插入表中的数据并不会写入视图，会造成数据的丢失。）——这块语焉不详，到底是不使用该参数创建后就永远不会有以前数据了，还是说只是源表不插入的时候没有，等有插入动作后，将历史数据一并传过来？

一般原则：
- 在创建 MV 表时，一定要使用 TO 关键字为 MV 表指定存储位置，否则不支持 嵌套视图(多个物化视图继续聚合一个新的视图)
- 在创建 MV 表时如果用到了多表联查，不能为连接表指定别名，如果多个连接表中存在同名字段，在连接表的查询语句中使用 AS 将字段名区分开
- 在创建 MV 表时如果用到了多表联查，只有当第一个查询的表有数据插入时，这个 MV 才会被触发
- 在创建 MV 表时不要使用 POPULATE 关键字，而是在 MV 表建好之后将数据手动导入 MV 表
- 在使用 MV 的聚合引擎时，也需要按照聚合查询来写 sql，因为聚合时机不可控

#### Load
```
 -- clickhouse
 CREATE TABLE person(
   idcard TEXT,
   name TEXT,
   address TEXT,
   tel TEXT,
   email TEXT,
   idcard_type TEXT,
   ct TEXT,
   seq INT
 )
 engine = MergeTree()
 order by (idcard)
 ;
```
```
 time clickhouse-client -u default --password --database="default" --query="insert into default.person_all FORMAT CSVWithNames" < person.csv
```
