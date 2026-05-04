---
source_title: Tar
categories:
- Develop
- Linux
last_modified: '2025-06-20T02:10:13Z'
---
Linux tar（tape archive）是 Linux/Unix 系统中用于归档文件和目录的强大命令行工具。

tar 名字来自 "tape archive"（磁带归档），最初用于将文件打包到磁带设备中，现在广泛用于在文件系统中打包和压缩文件。

tar 通常用于将多个文件和目录打包成一个归档文件，称为 "tarball"（通常带有 .tar 扩展名）。

tar 本身不压缩文件，但可以与压缩工具（如 gzip 或 bzip2）结合使用，创建压缩的归档文件（如 .tar.gz 或 .tar.bz2）。

### 语法
```
 tar [options] -f archive.tar [files...]
```

### options
- -c：创建一个新的归档文件
- -x：解压归档文件
- -t：列出归档文件的内容
- -r：向现有归档文件中追加文件(追加仅支持 .tar，下同)
- -u：仅追加比归档文件中已有文件更新的文件
- -d：找到归档文件中与文件系统不同步的差异
- -A：将一个 .tar 文件追加到另一个 .tar 文件中

### 选择和排除
- -C：切换到指定目录进行操作(可用做去除绝对路径)
- --exclude=：排除匹配指定模式的文件/目录
- --exclude-from=：从指定文件读取要排除的模式
- --exclude-vcs：排除版本控制系统生成的文件（如 .git、.svn 等）

### 压缩
- -z：使用 gzip 压缩归档文件
- -J：使用 xz 压缩归档文件

### 输出
- -v：显示详细操作过程（verbose）

### 权限
- -p：保留文件的原始权限（解压时）
- --no-same-owner：不设置文件所有者

### Note

#### md5

正常情况下 tar + gzip，即使打包文件未改变，其 md5 也不同。
- Q: 目录中的文件未修改，打包压缩后MD5不同
```
 -rw-r--r-- 1 bi oinstall 561370 Jul 23 04:00 pangolin_20220723.tar.gz
 -rw-r--r-- 1 bi oinstall 561370 Jul 24 04:00 pangolin_20220724.tar.gz
 -rw-r--r-- 1 bi oinstall 561370 Jul 25 04:00 pangolin_20220725.tar.gz
```
 
```
 857c2b83550839a8f2c1df202c197c35 pangolin_20220723.tar.gz
 93aa0e9ea72aa9dbbd5b8ef4ef5838bd pangolin_20220724.tar.gz
 1d924a38da3fc3ca0af085f832624890 pangolin_20220725.tar.gz
```
- W: gzip 压缩时，默认保存原来的文件名称及时间戳
- A: 将 tar -czvf 分为两步，打包，压缩，而 gzip 时增加参数：-n 即可不保存原来的文件名称及时间戳
```
 tar -cvf /u01/source/${PT}_${DAYID}.tar /home/bi/${PT}/ --exclude-from /home/bi/${PT}/excludelist
 gzip -n /u01/source/${PT}_${DAYID}.tar
```

通过管道操作合并成一条如下
```
 tar -cvf - /home/bi/${PT}/ --exclude-from /home/bi/${PT}/excludelist | gzip -n > /u01/source/${PT}_${DAYID}.tar.gz
```
- -cvf -: 这里的 -f 后面跟一个 -（连字符），表示 tar 命令的输出不是写入一个文件，而是写入标准输出 (stdout)
- -n: 这个选项告诉 gzip 在压缩后的文件名中不保存原始文件的名称和时间戳。当 gzip 从管道读取输入时，这个选项通常是推荐的，因为它不会尝试根据输入文件名来命名输出文件

#### 去除目录前缀
```
 # 将 /root/log/monitor/202408/ 下文件打包，不含任何目录
 tar -czvf monitor_202408.tar.gz -C/root/log/monitor/202408/ .
 # 将 /root/log/monitor/202408/ 下文件打包，含 202408 目录
 tar -czvf monitor_202408.tar.gz -C/root/log/monitor/ 202408/.
 # 暂未发现如何选取文件，如：*.log 等(2024/12/6) -> 已解决(2025/5/16)
 # 选取文件: 使用 --transform='s|.*/||' 路径替换功能，把路径前缀全部去掉，仅保留文件名
 tar -czvf /u01/web/soft/phpbb_mwbbs.tar.gz --transform='s|.*/||' /u01/backup/phpbb/*`date +%Y%m%d`*
```
