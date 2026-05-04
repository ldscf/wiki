---
source_title: Doris DBA
categories:
- DB
- Develop
- Doris
last_modified: '2024-03-01T03:02:57Z'
---
#### 数据库
```
 # show databases;
 create database tidat;
```

#### 用户
```
 create user bi identified by 'password';
 -- set password for bi = password('abcd1234');
 grant all on bidb to bi;
```

#### 连接数
```
 -- set property for 'bi' 'max_user_connections' = '200';
 show property for 'bi' like '%max_user_connections%';
```

#### 导出文件
```
 # fe.conf
 enable_outfile_to_local=true
```
```
 # set enable_parallel_outfile = false;
 # 对于普通路径，false=随机一个节点全量，true=每节点部分量
 SELECT   identity_code,
          cast(identity_info as string) identity_info
 FROM     bi.ti_f_identity 
 where    part = 3
 limit 30000000
 INTO OUTFILE "file:///tmp/result000_"
```

#### SYS INFO

##### FE, BE
```
 show proc '/frontends'
 show proc '/backends'
```
 
```
 SHOW PROPERTY FOR 'bi' LIKE '%max_user_connections%';
 -- SET PROPERTY FOR 'bi' 'max_user_connections' = '200';
 SHOW PROPERTY FOR 'bi' LIKE '%max_query_instances%';
 SHOW PROPERTY FOR 'bi' LIKE '%qe_max_connection%';
```

##### 查看当前连接数
```
 show full processlist;
 show processlist;
```
