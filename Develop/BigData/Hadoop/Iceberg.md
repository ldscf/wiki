---
source_title: Iceberg
categories:
- Develop
- Hadoop
- Hive
- Iceberg
last_modified: '2024-12-24T03:16:48Z'
---
![Iceberg-logo.png](https://mwbbs.eu.org/wiki/images/c/ca/Iceberg-logo.png)

Apache Iceberg™ is a high-performance format for huge analytic tables. Iceberg brings the reliability and simplicity of SQL tables to big data, while making it possible for engines like Spark, Trino, Flink, Presto, Hive and Impala to safely work with the same tables, at the same time.

为了解决数据存储和计算引擎之间的适配的问题，Netflix 开发了 Iceberg，2018 年 11 月 16 日进入 Apache 孵化器，2020 年 5 月 19 日从孵化器毕业，成为 Apache的顶级项目。

Iceberg 是一个面向海量数据分析场景的开放表格式（Table Format）。表格式（Table Format）可以理解为元数据以及数据文件的一种组织方式，处于计算框架（Flink，Spark，Hive）之下，数据文件之上。

### Expressive SQL

Iceberg supports flexible SQL commands to merge new data, update existing rows, and perform targeted deletes. Iceberg can eagerly rewrite data files for read performance, or it can use delete deltas for faster updates.> MERGE INTO prod.nyc.taxis pt

USING (SELECT * FROM staging.nyc.taxis) st

ON pt.id = st.id

WHEN NOT MATCHED THEN INSERT *;

### Full Schema Evolution

Schema evolution just works. Adding a column won't bring back "zombie" data. Columns can be renamed and reordered. Best of all, schema changes never require rewriting your table.> ALTER TABLE taxisRENAME COLUMN trip_distanceTO distance;

### Hidden Partitioning

Iceberg handles the tedious and error-prone task of producing partition values for rows in a table and skips unnecessary partitions and files automatically. No extra filters are needed for fast queries, and table layout can be updated as data or queries change.

### Time Travel and Rollback

Time-travel enables reproducible queries that use exactly the same table snapshot, or lets users easily examine changes. Version rollback allows users to quickly correct problems by resetting tables to a good state.> SELECT count(*) FROM nyc.taxis FOR TIMESTAMP AS OF TIMESTAMP '2022-01-01 00:00:00.000000 Z';

### Data Compaction

Data compaction is supported out-of-the-box and you can choose from different rewrite strategies such as bin-packing or sorting to optimize file layout and size.> CALL system.rewrite_data_files("nyc.taxis");
