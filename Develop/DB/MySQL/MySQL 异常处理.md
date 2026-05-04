---
source_title: MySQL 异常处理
categories:
- DB
- Develop
- MySQL
last_modified: '2022-12-30T07:55:36Z'
---
MySQL 异常处理

### 定义异常处理

DECLARE ... HANDLER, 必须出现在变量或条件声明的后面

当某个错误（condition_value）发生时，执行指定的语句（statement），执行完之后再决定如何操作（action）

DECLARE action HANDLER FOR condition_value [, condition_value, ...]
```
  statement
```

##### action
```
   CONTINUE | EXIT  # exit 只退出当前 begin end 
```

##### condition_value
- mysql_error_code# MySQL 自定义的错误代码
- SQLSTATE [VALUE]# ANSI SQL和 ODBC 的标准化的错误代码
- condition_name  # 
- SQLWARNING      # 01 开头sqlstate码
- NOT FOUND       # 02 开头sqlstate码，数据不存在的错误，如在使用游标或者SELECT无数据
- SQLEXCEPTION    # 除‘01’或‘02’开头的所有sqlstate码

如：ERROR 1045 (28000): Access denied for user 'admin'@'m02' (using password: NO)

DECLARE CONTINUE HANDLER FOR SQLSTATE '23000' SET error = '23000';

DECLARE CONTINUE HANDLER FOR SQLSTATE '21S01' SET error = '21S01';

DECLARE CONTINUE HANDLER FOR 1136 SET error = '21S01';     # 错误编号和SQLSTATE码

DECLARE CONTINUE HANDLER FOR SQLWARNING BEGIN END;             # 忽略某类错误

DECLARE EXIT HANDLER FOR SQLWARNING, NOT FOUND, SQLEXCEPTION ... # 获取所有异常

DECLARE CONTINUE HANDLER FOR NOT FOUND set error = 1;        # 忽略某类错误，置错误状态

### 错误处理器的优先级

当有多个错误处理器都满足特定错误条件的时候，MySQL将按更明确者优先的原则决定优先级。

MySQL中的每个错误都会映射到一个特定的错误码，因此错误码是最明确的。一个 SQLSTATE 可以对应到多个 MySQL 错误码，所以没那么明确。SQLEXCEPTION 和 SQLWARNING 分别指代的是 SQLSTATES 中类型相近的一组值，所以它的明确性最低。

基于错误处理器的优先级规则，MySQL 错误码处理器，SQLSTATE 错误处理器 和 SQLEXCEPTION错误处理器顺序上分别排在1、2、3位。
