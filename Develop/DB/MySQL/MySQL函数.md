---
source_title: MySQL函数
categories:
- DB
- Develop
- MySQL
last_modified: '2023-11-10T13:22:22Z'
---
### 变量

#### 用户定义的变量

是一种以@符号为前缀的变量类型。

##### 定义
```
 SET @var_name = value;
 **- OR -**
 SELECT @var_name := value;
 **- SQL -**
 SELECT @age_max:= MAX(age) FROM students;
```

##### 使用
```
 SELECT @var_name
 SELECT * FROM students WHERE age = @maxage;
```

未定义变量输出NULL

#### 局部变量

局部变量是强类型变量，作用域在声明它的存储过程块中。无 DEFAULT 子句，变量将被赋予初始值 NULL。
```
 DECLARE i, j INT DEFAULT 0;
```

#### 系统变量

MySQL 包含各种配置其操作的系统变量，每个系统变量都有一个默认值。我们可以通过在运行时使用 SET 语句动态地更改某些系统变量，使得在不停止和重新启动服务器的情况下修改系统变量。

MySQL服务器提供了许多系统变量，如GLOBAL、SESSION或MIX类型。全局变量在服务器的整个生命周期中可见，而会话变量仅在特定会话中保持活动状态。
```
 SELECT @@key_buffer_size;
```

### 函数

#### 常用函数

| Name | Description |
| CONCAT(s1, s2, .....) | 将字符串s1, s2等多个字符串合并为一个字符串 |
| TRIM(s) | 去掉字符串s开始处和结尾处的空格 |
| STRCMP(s1, s2) | 比较字符串s1和s2 |
| SUBSTRING(s, n, len) | 获取从字符串s中的第n个位置开始长度为len的字符串 |
| INSTR(s, s1) | 从字符串s中获取s1的开始位置 |
| REVERSE(s) | 将字符串s的顺序反过来 |
| RAND() | 返回0~1的随机数 |
| NOW() | 返回当前日期和时间 |
| DATE_FORMAT(d, f) | 按照表达式f的要求显示日期d, date_format(now(), '%Y%m%d%H%m%S') |
| INET_ATON(IP) | 将IP地址转换为数字表示，IP值需要加上引号 |
| INET_NTOA(n) | 可以将数字n转换成IP的形式 |
| GET_LOCT(name, time) | 加锁函数，定义一个名称为name、持续时间长度为time秒的锁，如果锁定成功，返回1，如果尝试超时，返回0，如果遇到错误，返回NULL. |
| RELEASE_LOCK(name) | 解除名称为name的锁，如果解锁成功，返回1，如果尝试超时，返回0，如果解锁失败，返回NULL。 |

#### 字符串函数

字符串连接、字符串比较、大小写转换、获取子串等。

| Name | Description |
| CHAR_LENGTH(s) | 返回字符串s的字符数 |
| LENGTH(s) | 返回字符串s的长度 |
| **CONCAT(s1, s2, .....)** | **将字符串s1, s2等多个字符串合并为一个字符串** |
| CONCAT_WS(x, s1, s2, ....) | 同COUCAT(s1, s2, .....)，但是每个字符串之间要加上x |
| INSERT(s1, x, len, s2) | 将字符串s2替换s1的x位置开始长度为len的字符串 |
| UPPER(s), UCASE(s) | 讲字符串s的所有字符都变成大写字母 |
| LOWER(s), LCASE(s) | 讲字符串s的所有字符都变成小写字母 |
| LEFT(s, n) | 返回字符串s的前n个字符 |
| RIGHT(s, n) | 返回字符串s的后n个字符 |
| LPAD(s1, len, s2) | 字符串s2来填充s1的开始处，使字符串长度达到len |
| RPAD(s1, len, s2) | 字符串s2来填充s1的结尾处，使字符串长度达到len |
| LTRIM(s) | 去掉字符串s开始处的空格 |
| RTRIM(s) | 去掉字符串s结尾处的空格 |
| **TRIM(s)** | **去掉字符串s开始处和结尾处的空格** |
| TRIM(s1 FROM s) | 去掉字符串s中开始处和结尾处的字符串s1 |
| REPEAT(s, n) | 将字符串s重复n次 |
| SPACE(n) | 返回n个空格 |
| REPLACE(s, s1, s2) | 用字符串s2代替字符串s中的字符串s1 |
| **STRCMP(s1, s2)** | **比较字符串s1和s2** |
| **SUBSTRING(s, n, len)** | **获取从字符串s中的第n个位置开始长度为len的字符串** |
| MID(s, n, len) | 同SUBSTRING(s, n, len)ATE(s1, s), POSTTION(s1 IN s)从字符串s中获取s1的开始位置 |
| **INSTR(s, s1)** | **从字符串s中获取s1的开始位置** |
| **REVERSE(s)** | **将字符串s的顺序反过来** |
| ELT(n, s1, s2...) | 返回第n个字符串 |
| FIELD(s, s1, s2...) | 返回第一个与字符串s匹配的字符串的位置 |
| FIND_IN_SET(s1, s2) | 返回在字符串s2中与s1匹配的字符串的位置 |
| MAKE_SET(x, s1, s2...) | 按x的二进制数从s1, s2......sn中选取字符串 |

#### 数学函数

绝对值函数、正弦函数、余弦函数、获取随机数等。

| Name | Description |
| ABS(x) | 返回x的绝对值 |
| CEIL(x), CEILING(x) | 返回大于或等于x的最小整数（向上取整） |
| FLOOR(x) | 返回小于或等于x的最大整数（向下取整） |
| **RAND()** | **返回0~1的随机数** |
| RAND(x) | 返回0~1的随机数，x值相同时返回的随机数相同 |
| SIGN(x) | 返回x的符号，x是负数、0、正数分别返回-1、0、1 |
| PI() | 返回圆周率 |
| TRUNCATE(x, y) | 返回数值x保留到小数点后y位的值 |
| ROUND(x) | 返回离x最近的整数（四舍五入） |
| ROUND(x, y) | 保留x小数点后y位的值，但截断时要四舍五入 |
| POW(x, y), POWER(x, y) | 返回x的y次方 |
| SQRT(x) | 返回x的平方根 |
| EXP(x) | 返回e的x次方 |
| MOD(x, y) | 返回x 除以y以后的余数 |
| LOG(x) | 返回自然对数（以e为底的对数） |
| LOG10(x) | 返回以10为底的对数 |
| RADIANS(x) | 讲角度转换为弧度 |
| DEGREES(x) | 讲弧度转换为角度 |
| SIN(x) | 求正弦值 |
| ASIN(x) | 求反正弦值 |
| COS(x) | 求余弦值 |
| ACOS(x) | 求反余弦值 |
| TAN(x) | 求正切值 |
| ATAN(x), ATAN(x, y) | 求反正切值 |
| COT(x) | 求余切值 |

#### 时间函数

取当前时间、获取当前日期、返回年份、返回日期等。

| Name | Description |
| CURDATE(), CURRENT_DATE() | 返回当前日期 |
| CURTIME(), CURRENT_TIME() | 返回当前时间 |
| **NOW()**, CURRENT_TIMESTAMP(), LOCALTIME(), SYSDATE(), LOCALTIMESTAMP() | **返回当前日期和时间** |
| UNIX_TIMESTAMP() | 以UNIX时间戳的形式返回当前时间 |
| UNIX_TIMESTAMP(d) | 将时间d以UNIX时间戳的形式返回 |
| FROM_UNIXTIME(d) | 把UNIX时间戳的时间转换为普通格式的时间 |
| UTC_DATE() | 返回UTC(国际协调时间)日期 |
| UTC_TIME() | 返回UTC时间 |
| MONTH(d) | 返回日期d中的月份值，范围是1~12 |
| MONTHNAME(d) | 返回日期d中的月份名称，如january |
| DAYNAME(d) | 返回日期d是星期几，如Monday |
| DAYOFWEEK(d) | 返回日期d是星期几，1表示星期日，2表示星期2 |
| WEEKDAY(d) | 返回日期d是星期几，0表示星期一,1表示星期2 |
| WEEK(d) | 计算日期d是本年的第几个星期，范围是0-53 |
| WEEKOFYEAR(d) | 计算日期d是本年的第几个星期，范围是1-53 |
| DAYOFYEAR(d) | 计算日期d是本年的第几天 |
| DAYOFMONTH(d) | 计算日期d是本月的第几天 |
| YEAR(d) | 返回日期d中的年份值 |
| QUARTER(d) | 返回日期d是第几季度，范围1-4 |
| HOUR(t) | 返回时间t中的小时值 |
| MINUTE(t) | 返回时间t中的分钟值 |
| SECOND(t) | 返回时间t中的秒钟值 |
| EXTRACT(type FROM d) | 从日期d中获取指定的值，type指定返回的值，如YEAR, HOUR等 |
| TIME_TO_SEC(t) | 将时间t转换为秒 |
| SEC_TO_TIME(s) | 将以秒为单位的时间s转换为时分秒的格式 |
| TO_DAYS(d) | 计算日期d到0000年1月1日的天数 |
| FROM_DAYS(n) | 计算从0000年1月1日开始n天后的日期 |
| DATEDIFF(d1, d2) | 计算日期d1到d2之间相隔的天数 |
| TIMESTAMPDIFF(type, d1, d2) | 计算日期d1到d2之间的时间差，type可指定YEAR、MONTH、DAY、HOUR、MINUTE或SECOND。 |
| ADDDATE(d, n) | 计算开始日期d加上n天的日期 |
| ADDDATE(d, INTERVAL expr type) | 计算起始日期d加上一个时间段后的日期 |
| SUBDATE(d, n) | 计算起始日期d减去n天的日期 |
| SUBDATE(d, INTERVAL expr type) | 计算起始日期d减去一个时间段后的日期 |
| ADDTIME(t, n) | 计算起始时间t加上n秒的时间 |
| SUBTIME(t, n) | 计算起始时间t减去n秒的时间 |
| **DATE_FORMAT(d, f)** | **按照表达式f的要求显示日期d, date_format(now(), '%Y%m%d%H%m%S')** |
| TIME_FORMAT(t, f) | 按照表达式f的要求显示时间t |
| GET_FORMAT(type, s) | 根据字符串s获取type类型数据的显示格式 |

#### JSON函数

| Name | Description |
| JSON_ARRAY(d1,d2...) | 创建JSON数组 |
| JSON_ARRAY_APPEND(json_arr, path, value) | 往json数组内之后追加元素 |
| JSON_ARRAY_INSERT(json_arr, path, value) | 往json数组内之前添加元素 |
| JSON_CONTAINS() | JSON是否在路径中包含特定对象 |
| JSON_CONTAINS_PATH() | JSON是否在路径处包含任何数据 |
| JSON_DEPTH() | JSON的最大深度 |
| JSON_EXTRACT() | 从JSON获取数据 |
| JSON_INSERT() | 如果数据不存在就将数据插入JSON |
| JSON_KEYS() | JSON文档中的键数组（类似map.keys()） |
| JSON_LENGTH() | JSON文档中的元素数量 |
| JSON_MERGE_PATCH() | 合并JSON，替换重复键的值(后面替换前面) |
| JSON_MERGE_PRESERVE() | 合并JSON，保留重复的键 |
| JSON_OBJECT() | 创建JSON |
| JSON_PRETTY() | 格式化打印JSON |
| JSON_QUOTE() | 引用JSON(将字符转换成带""，能转换转译字符) |
| JSON_REMOVE() | 从JSON文档中删除数据 |
| JSON_REPLACE() | 替换JSON文档中的值 |
| JSON_SEARCH() | 查询JSON文档中值的路径 |
| JSON_SET() | 将数据插入JSON文档（存在即替换值） |
| JSON_STORAGE_SIZE() | JSON二进制所占字节数 |
| JSON_TYPE() | JSON值的类型 |
| JSON_UNQUOTE() | 引用JSON(去除字符的""，能转换转译字符) |
| SON_VALID() | JSON值是否有效 |

#### 系统信息函数

获取数据库名、获取当前用户、获取数据库版本等。

| Name | Description |
| VERSION() | 返回数据库的版本号 |
| CONNECTION_ID() | 返回服务器的连接数，也就是到现在为止mysql服务的连接数 |
| DATABASE(), SCHEMA() | 返回当前数据库名 |
| USER() | 返回当前用户的名称 |
| CHARSET(str) | 返回字符串str的字符集 |
| COLLATION(str) | 返回字符串str的字符排列方式 |
| LAST_INSERT_ID() | 返回最后生成的auto_increment值 |

#### 加密函数

字符串加解密、Hash 等。

| Name | Description |
| PASSWORD(str) | 对字符串str进行加密 |
| MD5(str) | 对字符串str进行加密 |
| ENCODE(str, pswd_str) | 使用字符串pswd_str来加密字符串str，加密结果是一个二进制数，必须使用BLOB类型来保持它 |
| DECODE(crypt_str, pswd_str) | 解密函数，使用字符串pswd_str来为crypt_str解密 |

#### 其他函数

格式化、编码转换、类型转换、加锁、条件选择等。

| Name | Description |
| FORMAT(x, n) | 格式化函数，可以讲数字x进行格式化，将x保留到小数点后n位，这个过程需要进行四舍五入。 |
| ASCII(s) | 返回字符串s的第一个字符的ASSCII码 |
| BIN(x) | 返回x的二进制编码 |
| HEX(x) | 返回x的十六进制编码 |
| OCT(x) | 返回x的八进制编码 |
| CONV(x, f1, f2) | 将x从f1进制数变成f2进制数 |
| **INET_ATON(IP)** | **将IP地址转换为数字表示，IP值需要加上引号** |
| **INET_NTOA(n)** | **可以将数字n转换成IP的形式** |
| **GET_LOCT(name, time)** | **加锁函数，定义一个名称为name、持续时间长度为time秒的锁，如果锁定成功，返回1，如果尝试超时，返回0，如果遇到错误，返回NULL.** |
| **RELEASE_LOCK(name)** | **解除名称为name的锁，如果解锁成功，返回1，如果尝试超时，返回0，如果解锁失败，返回NULL。** |
| IS_FREE_LOCK(name) | 判断是否使用名为name的锁，如果使用，返回0，否则返回1. |
| CONVERT(s USING cs) | 将字符串s的字符集变成cs |
| CAST(x AS type),CONVERT(x, type) | 这两个函数将x变成type类型，这两个函数只对BINARY,CHAR,DATE,DATETIME,TIME,SIGNED  INTEGER,UNSIGNED INTEGER这些类型起作用，但这两种方法只是改变了输出值得数据类型，并没有改变表中字段的类型。 |
