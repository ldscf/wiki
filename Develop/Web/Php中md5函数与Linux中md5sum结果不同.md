---
source_title: Php中md5函数与Linux中md5sum结果不同
categories:
- Develop
- Php
- Web
last_modified: '2023-02-12T13:54:11Z'
---

Php中md5函数与Linux中md5sum结果不同，MySQL也同样，其MD5函数算出来的值与Linux中md5sum结果也不同。

Linux中用 echo 字符串| md5sum 或 md5sum 文件名，两种方式来计算的md5值，字符串最后含有隐含的字符串终止符，所以并非只计算了字符串的md5值。

### 方法一

如果是字符串可以通过增加-n参数解决： 

echo -n '123'| md5sum

#### PHP

<?php
```
   echo md5('123');
```

?>

#### Linux

echo -n '123'|md5sum

#### MySQL

SELECT MD5('123');> 202cb962ac59075b964b07152d234b70

也可以在PHP或MySQL中增加回车符：

### 方法二

#### PHP

<?php
```
   echo md5(“123\n”);
```

?>

注意上面md5中的字符串必须为双引号。

#### Linux

echo '123'|md5sum> ba1f2511fc30423bdbb183fe33f3dd0f
