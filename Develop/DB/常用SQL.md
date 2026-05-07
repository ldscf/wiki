---
source_title: 常用SQL
categories:
- DB
- Develop
last_modified: '2024-01-22T05:13:59Z'
---
#### 空值转为 0
- MySQL
```
 case when (col > '') then col else 0 end
```

#### 固定长度数字
- MySQL
```
 substr(concat('00000', floor(rand() * 1000000)), -6)
```

#### 近一百天内时间
- Doris
```
 date_add(now(), - rand() * 100)
```
- MySQL
```
 date_add(now(), interval - RAND() * 100 day)
```

#### 生成连续数字
- MySQL
```
 with recursive seq(no) as(
```

     select   1  no

     union all

     select   no+1  no

     from     seq

     where    no < 100
```
 )
 select   no
 from     seq
```
- MySQL, Doris, Oracle
```
 with t1 as (
```

    with a as (

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL 

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5

    ),

    b as (

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL 

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5

    )

    select 1 from a, b
```
 ),
 t2 as (
```

    with a as (

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL 

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5

    ),

    b as (

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL 

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5

    )

    select 1 from a, b
```
 ),
 t3 as (
```

    with a as (

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL 

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5

    ),

    b as (

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5 UNION ALL 

       SELECT 1 UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5

    )

    select 1 from a, b

    limit 100
```
 )
 select 1 from t1, t2, t3
```
