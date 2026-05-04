---
source_title: SQL优化
categories:
- DB
- Develop
last_modified: '2023-12-07T05:43:38Z'
---
### 索引

#### 使用索引优化查询条件及排序字段

ti_f_identity 近两百万数据，原查询需要 3 秒。建立索引后，小于 0.1 秒（以下未注明时间）。
```
 create index i_ti_f_identity_env_reg on ti_f_identity(environment_id, register_at, identity_code);
```
 
```
 SELECT   *
 FROM     ti_f_identity
 WHERE    register_state = 3
 AND      environment_id = '1722454762444832770'
 ORDER BY register_at DESC 
 LIMIT 10
```
 
```
 SELECT   *
 FROM     ti_f_identity
 WHERE    1=1
 and      register_state = 2
 AND      environment_id = 'ABC123'
 ORDER BY register_at DESC 
 LIMIT 10
```
```
 SELECT   *
 FROM     ti_f_identity
 WHERE    1=1
 and      register_state = 2
 and      template_code  = '42cbd45b-f03b-4840-bdb0-965238c4c071'
 AND      environment_id = 'ABC123'
 ORDER BY register_at DESC 
 LIMIT 10
 -- 这个 SQL 执行时间是 1.6 秒。
```
 
```
 SELECT   *
 FROM     ti_f_identity
 WHERE    1=1
 and      register_state = 2
 and      identity_code  like '88.102.111/42cbd45b-f03b-4840-bdb0-965238c4c071%'
 AND      environment_id = 'ABC123'
 ORDER BY register_at DESC 
 LIMIT 10
```
