---
source_title: Pandas read sql
categories:
- Develop
- Pandas
- Python
last_modified: '2022-12-29T07:39:48Z'
---
Read SQL query or database table into a DataFrame.

[pandas.read_sql](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_sql.html#pandas.read_sql)(sql, con, index_col=None, coerce_float=True, params=None, parse_dates=None, columns=None, chunksize=None)

#### Parameters
- sql
  - str or SQLAlchemy Selectable (select or text object)
  - SQL query to be executed or a table name.
- con
  - SQLAlchemy connectable, str, or sqlite3 connection
  - Using SQLAlchemy makes it possible to use any DB supported by that library. If a DBAPI2 object, only sqlite3 is supported. The user is responsible for engine disposal and connection closure for the SQLAlchemy connectable; str connections are closed automatically.

#### Examples
```
 import udb
 import ustr
 import pandas as pd
```
 
```
 d_db = ustr.ini2dict('bidb')
 ssql = "select * from bi.s_kv"
```
 
```
 conn = udb.db_conn(d_db, 2)
```
 
```
 df1 = pd.read_sql(ssql, conn)
```
 
```
     K_TYPE  K_ID          K_NAME                                              VALUE  ...    CA                  CT     UA    UT
 0     1002  0100            1101                                                 e9  ...  None 2017-04-13 13:48:32   None  None
 1     1001  0080          etl_e0  select   proc_id, proc_desc\r\nfrom     s_proc...  ...   sys 2017-05-03 19:43:41   None  None
 2     1002  0060            1006                                                 e6  ...  None 2017-05-04 11:25:53   None  None
 3     1001  0080        fill_sql  select   to_char(listagg(column_name, ',') wit...  ...   sys 2017-03-22 14:03:46   None  None
 4     1001  0080      c_scd_list  select db_name,owner,obj_type,obj_name from c_...  ...   sys 2017-03-01 23:34:00   None  None
 ..     ...   ...             ...                                                ...  ...   ...                 ...    ...   ...
 96    1001  4010        sms_ryyd  declare \n  v_cnt number;\nbegin\n  select cou...  ...   llc 2019-01-10 18:35:24   None  None
 97    1005  0010  rule_sensitive  in:[owner_card_id, customer_card_id, house_add...  ...   sys 2019-11-29 16:00:52   None  None
 98    0001  0011   log_keep_days                                                  3  ...   sys 2018-12-10 16:23:29   None  None
 99    1001  0210     d_buy_trace  declare\n  v_cnt number :=0;\n  i     number :...  ...   llc 2019-08-05 00:00:00   None  None
 100   1001  3052       bk_log_ct  declare\n   sn               varchar2(16);\n  ...  ...   sys 2018-12-24 15:25:46   None  None
```
 
```
 [101 rows x 10 columns]
```

#### P.S.
1. 连接数据库官方建议使用 sqlalchemy, 但使用其他的也可以
1. 写数据库不建议使用 to_sql, 局限性较大

#### See also

None
