---
source_title: Sqlite
categories:
- DB
- Develop
- OtherDB
last_modified: '2026-01-20T08:50:09Z'
---
SQLite3 安装及开发

### Install
```
 wget https://sqlite.org/2018/sqlite-autoconf-3220000.tar.gz
 tar xzvf sqlite-autoconf-3220000.tar.gz 
```
```
 ./configure --prefix=/usr/sqlite3
 make
 make install
```
```
 cd /usr/sqlite3/bin/
 sqlite3
```

### C++编译
```
 g++ t1.cpp -o t1 -lsqlite3 -L/usr/sqlite3/lib
```

### DBA

.import CSV TABLE

| 命令 | 说明 |
|:---|:---|
| + |
| 收缩文件 | vacuum | 在建表语句中，PRAGMA auto_vacuum = 1 |
| 显示版本 | version |
| 退出 | quit 或 exit |
| 导出全部表 | dump | sqlite3 database.db .dump > dump.sql |
| 导出指定表 | dump 表名 | 导出指定表的建表+数据脚本 |
| 导入 | sqlite3 new_database.db < dump.sql |
| 导入CSV | import | .separator "," OR .mode csv |
| 表名列表 | tables | 列出当前数据库所有表名 |
| 建表语句 | schema 表名 |
| 索引 | .indices 表名 |
| 输出表格 | mode box | 带边框的漂亮表格（sqlite 3.37+ 推荐） |
| 表结构 | select * from sqlite_master where type="table" and name="test"; |

### SQL

#### 分页

Sqlite 通过关键字 limit 限制
```
 select * from tw1_bht limit 10;           --显示10行
 select * from tw1_bht limit 1 offset 2;   --过滤2行，显示1行
 select * from tw1_bht limit 1, 2;         --过滤1行，显示2行
 select * from tw1_bht limit 1000000, 10;
```
 
```
 select count(*) from tw1_bht;
 select company_id, count(*) from tw1_bht group by company_id;
```

#### Sample
 ```
select   id             ||'|'||
         company_id.    ||'|'||
         group_id       ||'|'||
         shop_id        ||'|'||
         staff_team     ||'|'||
         house_id       ||'|'||
         trace_type     ||'|'||
         translate(trace_memo, '|'||chr(10)||chr(13), '/ ')||'|'||
         to_char(ct, 'yyyymmddhh24miss') ||'|'||
         staff_id       ||'|'||
         is_to_a        ||'|'||
         (house_id - trunc(house_id/100)*100)
from     dw.tw1_buy_house_trace
where    1=1
and      house_id - trunc(house_id/100)*100 = 0
create table tw1_bht (
  id number(22),
  company_id number(22),
  group_id number(22),
  shop_id number(22),
  staff_team number(22),
  house_id number(22),
  trace_type number(22),
  trace_memo varchar2(4000),
  ct number(14),
  staff_id number(22),
  is_to_a number(22),
  part_id number(2)
);
```

### ERROR

Error: database disk image is malformed

SQLite 数据库文件已损坏。这意味着文件的结构不再符合 SQLite 的预期，导致文件无法读取或不可靠。
```
 sqlite3 database.db "PRAGMA integrity_check;"
```

其它
```
 # 主要检查无序记录和丢失的页面
 qlite3 your_database.db "PRAGMA quick_check;"
```
 
```
 # 尝试从损坏的数据库中提取尽可能多的数据
 sqlite3 database.db ".recover" > recovered.sql
 sqlite3 new_database.db < recovered.sql
```
