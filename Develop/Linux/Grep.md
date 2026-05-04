---
source_title: Grep
categories:
- Develop
- Linux
- Shell
last_modified: '2025-08-05T08:46:55Z'
---
Linux grep(global regular expression)命令用于查找文件里符合条件的字符串或正则表达式，加上 -E 支持扩展的正则表达式(extended regular expressions)。

### Global

#### 行首行尾

使用 ^ 和 $ 符号来正则匹配输入行的开始或结尾
```
 df -h | grep '^File'
 File system      Size  Used Avail Use% Mounted on
```
 
```
 df -h | grep '/$'
 /dev/sda1        45G   38G  7.8G  83% /
```

#### 空行
```
 grep '^$'
```

#### 不区分大小写
```
 df -h | grep -i 'file'
```

#### 显示文件名及行号
```
 grep -rn 'import random' *py
 # -r 递归地搜索所有子目录
```

### Extended

#### 或
```
 ./rrun mc.list 'df -h' | grep -E '^-|/$'
```
