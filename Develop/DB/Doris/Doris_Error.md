---
source_title: Doris Error
categories:
- DB
- Develop
- Doris
last_modified: '2023-12-12T04:11:46Z'
---
#### [1105] Right expr of binary predicate should be value

删除数据时，右侧不支持表达式。如：
```
 DELETE FROM test WHERE part = abs( murmur_hash3_32 ('42cbd45b-f03b-4840-bdb0-965238c4c07c') % 10)
 -->
 DELETE FROM test WHERE part = 0
```

#### [1105] Table[XXX]'s state is not NORMAL. Do not allow doing ALTER ops

表的状态不正常，不允许执行 alter 操作。如一个表同时只能进行一个 Alter 任务，多个 alter 执行或连续执行就会报此错误。

一般不用特别处理，等下再执行即可。

#### [5025] Insert has filtered data in strict mode

空值不支持 ''，改为 NULL
```
 INSERT INTO ti_f_identity_reg_log (part, mm) VALUES (abs(murmur_hash3_32('c07c') % 10), '')
 -->
 INSERT INTO ti_f_identity_reg_log (part, mm) VALUES (abs(murmur_hash3_32('c07c') % 10), NULL)
```
