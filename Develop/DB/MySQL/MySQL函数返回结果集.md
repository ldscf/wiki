---
source_title: MySQL函数返回结果集
categories:
- DB
- Develop
- MySQL
last_modified: '2023-11-14T07:53:28Z'
---
MySQL 函数与过程均可返回结果集。

### 函数
```
 CREATE FUNCTION test_return_rs(
```

    date_b                    varchar, 

    date_e                    varchar
```
 )
```

    RETURNS TABLE

    LANGUAGE SQL

    DETERMINISTIC
```
 BEGIN
```

    -- ...
 
    -- 返回结果

    RETURN SELECT * FROM db1.test_return_rs;
```
 END
```

### 过程
```
 CREATE PROCEDURE test_return_rs(
```

    date_b                    varchar, 

    date_e                    varchar
```
 )
 BEGIN
```

    -- ...
 
    -- 返回结果

    SELECT * FROM db1.test_return_rs;
```
 END
```
