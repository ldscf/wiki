---
source_title: SQL 查询结果集总行数
categories:
- DB
- Develop
- MySQL
last_modified: '2025-12-16T07:48:18Z'
---
获取查询结果总行数的需求在各种应用场景中非常普遍，如：Web 应用分页、进度显示和预估等。
- 小数据量：COUNT(*) OVER()
- 超大数据量：分为两个查询（尤其是 WHERE 条件复杂时）：一个查询数据，另一个查总数（where 条件相同）

### COUNT(*) OVER()

窗口函数，可以在一次查询中同时获取数据和总数。
1. 性能：在大表上，COUNT(*) OVER() 可能比单独的 COUNT(*) 稍慢
1. 一致性：在事务中，窗口函数可确保数据的一致性
```
 SELECT   *, COUNT(*) OVER() AS _total FROM users LIMIT 10;
 SELECT   *, COUNT(*) OVER(PARTITION by dept_id) AS _total FROM users LIMIT 10;    # 可以按部门分别统计
```

#### 各数据库支持 COUNT(*) OVER() 情况对比

| 数据库 | 版本要求 |
|:---|:---|
| PostgreSQL | 8.4+ (2009) |
| MySQL | 8.0+ (2018) |
| SQL Server | 2005+ |
| Oracle | 8i+ (1999) |
| SQLite | 3.25.0+ (2018) |
| MariaDB | 10.2+ (2015) |
| DB2 | 9.7+ (2009) |
| Snowflake | 全部版本 |
| BigQuery | 全部版本 |
| Redshift | 全部版本 |

### 其他

#### MySQL

SQL_CALC_FOUND_ROWS + FOUND_ROWS()

这是 MySQL 专有的语法，旧版方式，在MySQL 8.0.17已标记为弃用，不推荐。
```
 SELECT **SQL_CALC_FOUND_ROWS** * FROM users LIMIT 10;
 SELECT **FOUND_ROWS()**;                              -- 立即获取 users 的总数
```
