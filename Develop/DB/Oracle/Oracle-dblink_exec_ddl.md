---
source_title: Oracle-dblink exec ddl
categories:
- DB
- Develop
- Oracle
last_modified: '2023-01-05T02:47:17Z'
---
  - Oracle - dblink exec_ddl**

Oracle 远程执行 DDL 语句，可以通过 dbms_utility 包中的过程完成，也可以自行在远程 DB 端定义一个过程。
```
 dbms_utility.EXEC_DDL_STATEMENT@dgdb22('create table bi.test(key number(10), val varchar2(100))')
```
```
 select * from v_dblink
```
```
 begin
```

    dbms_utility.EXEC_DDL_STATEMENT@dgdb22('create table bi.test(key number(10), val varchar2(100))');
```
 end;
```
```
 insert into bi.test@dgdb22 values(1001, 'Hello, World!');
 insert into bi.test@dgdb22 values(1002, 'Hi, Oracle.');
 commit;
 select * from bi.test@dgdb22;
```
```
 begin
```

    dbms_utility.EXEC_DDL_STATEMENT@dgdb22('truncate table bi.test');
```
 end;
```
