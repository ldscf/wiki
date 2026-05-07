---
source_title: Find
categories:
- Develop
- Linux
last_modified: '2026-05-06T14:15:01Z'
---
Linux find 命令用于在指定目录下查找文件和目录。

#### Format
```
 find [路径] [匹配条件] [动作]
```

Path: 查找的目录路径，可以是一个目录或文件名，也可以是多个路径，多个路径之间用空格分隔，如果未指定路径，则默认为当前目录

Expression: 可选参数，用于指定查找的条件，可以是文件名、文件类型、文件大小等等。
- 按文件名称查找: -name pattern (支持使用通配符 * 和 ?)
- 按文件类型查找: -type TYPE (TYPE = f(普通文件)、d(目录)、l(符号链接)等)
- 按文件大小查找: -size [+-]N[cwbkMG] (+/- 大于/小于，N 是一个整数c(字节)、w(字数)、b(块数)、k/M/G)
- 按修改时间查找: -mtime [+-]N (+/- 表示在指定天数前或后，N 是一个整数表示天数)(a=访问, c=状态变化, m=修改)
- 按访问时间查找: -amin n 查找在 n 分钟内被访问过的文件
- 指定目录层级  : -maxdepth 1 当前目录下一级

动作: 可选参数，用于对匹配到的文件执行操作，比如删除、复制等
- 列出完整路径: -exec ls -l {} \;  

注：{} 代表匹配到的文件名，\; 表示命令结束。
- 删除: -delete  

如：find . -name ".DS_Store" -type f **-delete**

表达式关系运算:
- 默认关系间是逻辑与，如：-mtime +0 -mtime -3 表示 1 天以上，4 天以下
- -o 逻辑并，如：-type f -mtime 0 -o -mtime 2 表示 第 1 天内，第 3 天内（无第 2 天内，以及第二项并未指定文件，有可能出现目录）

第 1 天内表示 [0-24)，第 3 天内表示 [48-72)

### Example

#### 删除七天前文件
```
 find /u01/app/archivelog/ -mtime +7 -type f |xargs rm -f;
 ## !! 不要使用 cd $PATH，再 find . 方法，在 $PATH 不存在时会出问题
 ---
 # 下面两个有问题: 加上 -name 之后，过滤出来的文件时间范围不对
 # Err : find /u01/app/archivelog -mtime +7 -type f -name *.arc -exec rm -f {} \;
 # Err : find /u01/app/archivelog -mtime +7 -type f -name *.arc |xargs rm -f;
```

#### 当前子目录增加执行权限
```
 # 适用于 http 目录 List 文件下载，无执行权限看不到目录等情况
 chmod +x `find . -type d`
```

#### 改变文件的权限
```
 chmod 644 `find . -type f`
 OR
 find . -type f | xargs chmod 644
 # 当文件名中有特殊字符时，通常上面的语句不能正确执行
 find . -type f -print0 | xargs -0 chmod 644
```

#### 在多台机器中查找大文件
```
 ./rrun ip_all "find / -type f -size +1G 2>/dev/null|grep '.zip'"
```
