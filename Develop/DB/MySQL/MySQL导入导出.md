---
source_title: MySQL导入导出
categories:
- DB
- Develop
- MySQL
last_modified: '2022-12-30T07:57:19Z'
---
MySQL 数据导入导出

### 配置

#### /etc/my.cnf

[mysqld]

secure_file_priv=

（1）'NULL'，表示禁止导入

（2）非空，只允许服务端该目录下文件（不含子目录）

（3）如果如上为空，则表示不限制目录

[client]

local_infile=1

# 是否支持本地文件，=0只允许服务端目录下文件（ERROR 3948 (42000): Loading local data is disabled; this must be enabled on both the client and server sides）

# 需要重启

#### 变量方式配置

# mysql>

SET GLOBAL local_infile=1;           #若 my.cnf 无配置

SHOW GLOBAL VARIABLES LIKE 'local_infile';

+---------------+-------+

| Variable_name | Value |
+---------------+-------+

| local_infile  | ON    |
+---------------+-------+

SHOW VARIABLES LIKE "secure_file_priv";

+------------------+-----------------------+

| Variable_name    | Value                 |
+------------------+-----------------------+

| secure_file_priv | /var/lib/mysql-files/ |
+------------------+-----------------------+

##### 定义后（& restart）：

+------------------+-------+

| Variable_name    | Value |
+------------------+-------+

| secure_file_priv |       |
+------------------+-------+

#### # 权限

# LOAD DATA

secure_file_priv

# mysqlimport

super, system_variables_admin, session_variables_admin

### mysqlimport  Ver 8.0.19

-d   --delete             First delete all rows from table.

-L   --local              Read all files through the client.
```
    --ignore-lines=#     Ignore first n lines of data infile.
```

-l   --lock-tables        Lock all tables for write (this disables threads).

-c   --columns=name      Use only these columns to import the data

若不指定 local，则只能从服务端 datadir 定义的目录下读取文件。

\072 为八进制 --> ':'

本地文件名必须与表名一致

#### # dump.txt --> psmm.mytbl

#### # secure_file_priv=

#### # 此时的默认导入目录：ERROR 13 (HY000): Can't get stat of '/var/lib/mysql/psmm/dump.txt' (OS errno 2 - No such file or directory)

# mytbl.sql

LOAD DATA INFILE 'dump.txt' INTO TABLE mytbl FIELDS TERMINATED BY ':' LINES TERMINATED BY '\n';

mysql -u root -Dpsmm < mytbl.sql

#### # 将 dump.txt 改名为 mytbl

mysqlimport -u root -p --fields-terminated-by="$(echo -ne '\072')" --lines-terminated-by="\n" psmm mytbl

#### # local file --> remote ip

#### # local_infile=1

mysqlimport -h n10 -u root -p --local psmm /root/mytbl.sql --fields-terminated-by="$(echo -ne '\072')" --lines-terminated-by="\n"

# 日期字段为空时，要么报错(load data方式)，要么置成'0000-00…'

## # 以上都是垃圾

#### # Export Data from oracle

select   PKID                                             ||chr(5)||
```
        CUST_CODE                                        ||chr(5)||
```
```
        to_char(RMD_CUST_TIME, 'yyyy-mm-dd hh24:mi:ss')   ||chr(5)||
```
```
        PLAN_DIRECTION                                    ||chr(5)||
```
```
        to_char(START_DATE, 'yyyy-mm-dd hh24:mi:ss')      ||chr(5)||
```
```
        to_char(END_DATE, 'yyyy-mm-dd hh24:mi:ss')        ||chr(5)||
```
```
        IMP_INFO                                         ||chr(5)||
```
```
        CREATED_BY                                       ||chr(5)||
```
```
        GRP_ID
```

from     scsales_prd.t_cm_look_plan

where    1=1

and      rownum < 10

;

# test.sql

# '\n' = X'0A'

LOAD DATA LOCAL
```
  INFILE '/u01/tmp/test.dat'
```
```
  INTO TABLE T_CM_CUST_HOLDER_TEST_1
```

FIELDS TERMINATED BY X'05'

LINES TERMINATED BY '\n'
```
  (@PKID, CUST_CODE, AGENT_ID, GROUP_ID, AREA_ID, BIG_AREA_ID, POOL_LEVEL, @CREATE_TIME, CREATE_BY, WARZONE_ID, SWZ_ID, @INPOOL_TIME, MARK, @UPDATE_TIME, UPDATE_BY, COMPANY_ID)
```

SET PKID = IF(@PKID = '', NULL, @PKID),
```
   CREATE_TIME = IF(@CREATE_TIME = '', NULL, @CREATE_TIME),
```
```
   INPOOL_TIME = IF(@INPOOL_TIME = '', NULL, @INPOOL_TIME),
```
```
   update_time = IF(@update_time = '', NULL, @update_time)
```

;

mysql -h n10 -Dpsmm -u root < test.sql

# pkid 为 int，当其为空时，默认置0.可以指定为空，但如果字段有非空限制，则为0

# varchar 字段，不需要指定

# LOG

-rw-r----- 1 mysql mysql    114688 May 18 14:28 T_CM_CUST_HOLDER_TEST_1.ibd

[root@m01 tmp]# time mysql -h n10 -Dpsmm -u root < test.sql

real    1m29.941s

user    0m0.147s

sys     0m0.565s

-rw-r----- 1 mysql mysql 1275068416 May 18 14:32 T_CM_CUST_HOLDER_TEST_1.ibd
