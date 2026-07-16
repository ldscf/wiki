---
source_title: Shell
categories:
- Develop
- Linux
- Shell
last_modified: '2026-01-23T03:13:15Z'
---
Shell 是一个用 C 语言编写的程序，是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

Ken Thompson 的 sh 是第一种 Unix Shell。

### 语法

#### 环境变量
```
 . ~/.bash_profile
 source .bash_profile
 # 上述一般在 bash 环境下会调用 .bashrc，默认有如下代码：
 if [ "$BASH" ]; then
```

   if [ -f ~/.bashrc ]; then

     . ~/.bashrc

   fi
```
 fi
```

#### 特殊变量
```
 echo [-ne][字符串]
 -n 不输出最后的 \n
 -e 启用反斜线转义
 -E 不启用反斜线转义
```

| 变量 | 含义 |
|:---|:---|
| $0 | 当前脚本的文件名 |
| $n | 传递给脚本或函数的参数。n 表示第几个参数 |
| $# | 传递给脚本或函数的参数个数 |
| $* | 传递给脚本或函数的所有参数 |
| $@ | 传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同 |
| $? | 上个命令的退出状态，或函数的返回值，0=OK |
| $$ | 当前 Shell 进程 ID。对于 Shell 脚本，就是这些脚本所在的进程ID |
| $0 | 含义 |
|:---|:---|
| 0 | 成功 |
| 1 | 一般性錯誤，命令失敗 | Operation not permitted |
| 2 | 命令参数或格式错误 | No such file or directory |
| 126 | 命令或脚本无执行权限 | Required key not available |
| 127 | 命令未找到 | Key has expired |
| 130 | 被用戶通过 Ctrl+C 終止（信号 2） | Owner died |
| 137 | 被 SIGKILL 信号终止（信号 9） | No more records (read after end of file) |

#### 转义
 ```
1. * 单引号，硬转义。所有的 shell 元字符、通配符都会被关掉。注意，硬转义中插入 '（单引号）的写法：'\*。
2. “” 双引号，软转义，只允许出现特定的 shell 元字符 $ ` \。$用于变量值替换、` 用于命令替换、\ 用于转义单个字符。
3. \ 反斜杠，转义，去除其后紧跟的元字符或通配符的特殊意义。
硬转义中插入 '（单引号）：
 curl -d '{"sql": "insert into TABLE values ('\*ABC'\*)"}' http://...
* 在 “” 中不用使用转义，但再次使用时，变量应该加“”，如：
 c="a * b"
 echo "$c"
防止本地通配符展开：
在 scp（以及大部分基于 Shell 的命令）中使用通配符 * 时，最容易引发的问题是 “本地通配符展开”（Local Shell Expansion）。如：
 scp bi@remote:~/Downloads/*.tar.gz .
本意是拷贝远程 Downloads 目录下的打包文件，但却在本地进行了通配符展开。此时需要使用双引号，传给远程服务器处理。
```

#### 与或非
- 与 && 或: -a
- 或 || 或: -o
- 非 !

##### 根据结果分支
```
 grep -q sse4_2 /proc/cpuinfo && echo "SSE 4.2 supported" || echo "SSE 4.2 not supported"
```

##### 在 Tar 文件中删除目录

其实就是使用 && 将三段命令组合起来
```
 FN=travel_20250625120001 && gunzip < ${FN}.tar.gz | tar --delete -f - travel/app/static/uploads/ | gzip > ${FN}_new.tar.gz && mv ${FN}_new.tar.gz ${FN}.tar.gz
```

#### 输出
- 错误(stderr)信息转为标准输出
```
 2>&1
```
- 标准输出(stdout)转为文件
```
 1>/tmp/log.txt
```

#### 将字符串作为shell命令执行
```
 res=`eval "${CMD}"`
```

#### 将结果作为字符串赋值给变量
```
 res=$(echo $ABC)
 REDIS_PASSWORD=$(kubectl get secret --namespace kube-nodelm redis -o jsonpath="{.data.redis-password}" | base64 -d)
```

#### 保持变量换行
```
 INFO=$(smem -U root) && echo "$INFO"
```

#### 排序保留第一行
```
 INFO=$(smem -U root) && echo "$INFO" | head -n 1 && echo "$INFO" | sed '1d' | sort -k7nr | head -n 10
```

#### 特殊字符
```
 PA=$(echo -ne '\001')
```

### 代码

#### Info
```
 ## m02(10.10.137.188)
 HOST=`hostname`
 IP=`ping $HOST -c1|xargs|awk -F')' '{print $1}'|awk -F'(' '{print $2}'`
 - OR -
 IP=`ifconfig | grep 'inet ' | grep -v 'inet 127.0.0.1 ' |awk '/broadcast|Bcast/' | awk '{print $2}'`
 echo "$HOST($IP)"
 # 内网IP
 ETH=$(ls /sys/class/net | awk '/^e/{print}')
 # 公网IP
 IPV4=$(curl ipv4.icanhazip.com) OR IPV4=$(curl ifconfig.me)
```

#### 时间
```
 ## 2022/08/29 16:23:01
 TIMEID=`date '+%Y/%m/%d %H:%M:%S'`
 DATEID=`date +%Y%m%d`
```
 
```
 DATEID=`date -dyesterday +%Y%m%d`
 DATEID=`date -dtomorrow +%Y%m%d`
 DATEID=`date -d "$date last month" +%Y%m%d`
 DATEID=`date -d "$date next month" +%Y%m%d`
```
 
```
 DATEID=`date -d "$date -1 day" +%Y%m%d`
 DATEID=`date -d "$date -1 month" +%Y%m%d`
```
 
```
 # date "+%Y-%m-%d %H:%M:%S `echo "abc"`"      # 比较常见的用法
```

#### 彩色输出
 ```

# echo -e "${RED}... : ... ${NC}" : 使用 ANSI 转义码（`\033[...]m`）实现彩色输出，提高信息的可读性。`-e` 选项用于解释转义序列。
RED='\033[0;31m'
YELLOW='\033[0;33m'
GREEN='\033[0;32m'
BLUE='\033[0;34m'
NC='\033[0m'
echo -e "${RED}Dele: $file, Size: $size, MD5: $current_md5${NC}"
echo -e "${YELLOW}Warning: MD5 collision (diff size) for '$file'. Retained. Size: $size, MD5: $current_md5${NC}"
```

#### 命令行参数判断
```
 if (( $# >= 1 )); then
```

     YM=$1
```
 else
```

     echo $0 'YM=yyyymm'

     exit 1
```
 fi
 YY=`echo $YM |cut -c 1-4`
```

#### 输入
```
 if [ "$1" == "" ];then
```

     echo "Not Parameter"
```
 fi
```
```
 if [ "$1" == "" ]; then
```

     CS=1
```
 #elif … ; then
 else
```

     CS=$1
```
 fi
```

#### 数值比较
```
 if ((${PS} > 0)); then
```

    echo "Task: $CMD exist."

    exit 1
```
 else
```

    echo OK
```
 fi
```

#### 字符比较
 ```
if [ "$2" == "N" ]; then
    echo "$2 == N"
else
    echo "$2 <> N"
fi
if [ "$2" != "N" ]; then
    echo "$2 <> N"
else
    echo "$2 == N"
fi

# [ ... ](#_..._) 是 Bash 语法，提供更强大的字符串匹配和更少的意外行为

## 可以进行不区分大小写的比较: if [ "${2,,}" != "n" ](#_"${2,,}"_!=_"n"_); then
if [ "$2" != "N" ](#_"$2"_!=_"N"_); then
    echo "$2 <> N"
else
    echo "$2 == N"
fi
```

#### current directory
```
 curdir=$( pwd )
```
 
```
 curdir="$(cd "$(dirname "${BASH_SOURCE[0]}")" &>/dev/null && pwd)"
 BASH_SOURCE[0] 等价于 BASH_SOURCE， 取得当前执行的 shell 文件所在的路径及文件名。
```
 
```
 上一级目录
 predir="$(
```

    cd ..

    pwd
```
 )"
```

#### file or path exist
```
 ## if file exist, run
 [ -f /etc/profile ] && source /etc/profile
 # if path not exist, create
 [ ! -d $VPATH ] && mkdir -p $VPATH
```
 
```
 -e filename 如果 filename存在，则为真
 -d filename 如果 filename为目录，则为真
 -f filename 如果 filename为常规文件，则为真
 -L filename 如果 filename为符号链接，则为真
 -r filename 如果 filename可读，则为真
 -w filename 如果 filename可写，则为真
 -x filename 如果 filename可执行，则为真
 -s filename 如果文件长度不为0，则为真
 -h filename 如果文件是软链接，则为真
```

#### 按行读文件
```
 while read LN
 do
```

     echo ${LN}
```
 done < ${LN_NAME}
```
 
```
 cat ${LN_NAME} | while read LN
 do
```

     echo $LN
```
 done
```
 
```
 for FN in ${LN_NAME}
 do
```

     echo ${FN}
```
 done
```

#### 遍历当前目录下的文件
```
 for FN in ${FILENAME}*
 do
```

     echo ${FN}
```
 done
```

#### 遍历当前目录下的目录
```
 find /u01/* -maxdepth 0 -type d
 # 遍历当前目录下的文件
 find /u01/* -maxdepth 0 -type f
```

#### 读变量列表

将本地多个文件复制到多台远程机器上。FN(需同步的本地文件列表)，TM(目标机器名称或IP)
 ```

# Copy files to remote machines
FN=(
  /home/hdfs/.bashrc
  /opt/tez/conf/tez-site.xml
  /opt/hadoop/etc/hadoop/tez-site.xml
  /opt/hive/conf/hive-site.xml
)
TM=(
  hdfs01
  hdfs02
  hdfs03
)

# source(local) -> target(TM)

# OK --> Sucessed
#
for F in ${FN[*]};do
  for T in ${TM[*]};do
    echo -n "Copy ${F} to ${T} : "
    scp -pq ${F} ${T}:${F} >/dev/null 2>&1 && echo "OK" || echo "Failed"
  done
done
```

将上面脚本改变一下，FN 定义可以放在用户登录的环境中，然后执行同步命令 hdcp。
 ```

## /bin/zhdcp

## Copy files to remote machines
#!/bin/sh
if [ "${FN}o" == "o" ];then
  echo 'FN is NULL.'
  exit 1
fi
TM=(
  hdfs01
  hdfs02
  hdfs03
)

# source(local) -> target(TM)

# OK --> Sucessed
#
for F in ${FN[*]};do
  for T in ${TM[*]};do
    echo -n "Copy ${F} to ${T} : "
    scp -pq ${F} ${T}:${F} >/dev/null 2>&1 && echo "OK" || echo "Failed"
  done
done
```

因为 FN 变量无法直接在 shell 中使用，故定义 alias:
```
 *alias hdcp='FN="${FN[@]}" zhdcp'*
```

FN=(A B C)相当于定义了一个数组，FN[0] 的值是 A，亦可以写作 FN。${FN[@]} 表示整个数组。

#### 循环
```
 CS=2
 for (( i=1; i<=${CS}; i++ ))
 do
```

    echo $i
```
 done
```

#### 字符串 子串
```
 # 包含
 [ $string =~ $sub](#_$string_=~_$sub)
 # 开头
 [ $string = $sub*](#_$string_=_$sub*)
 # 结束
 [ $string = *$sub](#_$string_=_*$sub)
```
 
```
 # 正则
 [ $string =~ ^.*$sub.*$](#_$string_=~_^.*$sub.*$)
 [ $string =~ ^$sub.*$](#_$string_=~_^$sub.*$)
 [ $string =~ ^.*$sub$](#_$string_=~_^.*$sub$)
```

#### 字符串 按分隔符取段
```
 # a bc 1 --> a bc
 STR1=${STR1% *}--> a bc
 STR1=${STR1%% *}  --> a
 #  a bc 1 --> bc 1
 STR1=${STR1#* }
 STR1=${STR1##* } --> 1
 # cut
 echo $STR1 |cut -d ' ' -f2
 echo $STR1 |cut -c 1-4
```

#### 计算
```
 V1 = $(expr $1 - $2)         # 须有空格
 let V1=$1-$2                 # 须无空格
```

#### 计算主机线程数-10
```
 threads=$(expr `cat /proc/cpuinfo |grep processor|wc -l` - 10)
 let c=`cat /proc/cpuinfo |grep processor|wc -l`-10
```

#### 特殊字符
```
 PA=$(echo -ne '\004')
```

#### 参数

#### 管道
```
 read VAR1                    # 可从标准输入或管道接收
```

#### sleep
```
 time sleep 0.030
```
 
```
 real    0m0.032s
 user    0m0.000s
 sys     0m0.002s
```

#### LOG Format

### 特别

#### 管道的右侧的变量

在 Bash 中使用管道 (|) 将一个命令的输出传递给 while read 循环时，管道的右侧（即 while read 循环及其内部）会在一个子 shell 中执行。这意味着当子 shell 结束时，在子 shell 中对变量的修改会随之消失。如下：
 ```
find "$directory" -type f | sort -V | \
while IFS= read -r line; do
    ((processed_files_count++)) # <-- 这里的修改不会传递到外部 shell
done

# 这里的 processed_files_count 仍然是循环开始前的值
```

其他：使用变量 files 保存 find 结果是可以的，问题在于 files 变量会存储整个输出，这在文件列表非常庞大时会成为一个显著的问题。如下：
 ```
files=$(find "$directory" -type f | sort -V)
while IFS= read -r file; do
  ((processed_files_count++))
done <<< "$files"
```

#### 进程替换

< <(...) 是 Bash 中非常强大而优雅的进程替换机制(Process Substitution)，可以将命令的输出结果模拟成文件输入，无需临时文件、效率高、可读性强，是 Bash 脚本中高级输入重定向的首选技巧之一。

例如：command < <(some_other_command) 会执行 some_other_command，并将它的标准输出表现为一个临时文件提供给 command 处理。避免了传统的管道方式：some_other_command | command，从匿名的、流式的 some_other_command 获取结果，在 command 中处理时（相当于 some_other_command 所在 shell 的子 shell）变量无法传递到外部的问题。如下：
 ```
while IFS= read -r line; do
  ((processed_files_count++))
done < <(find "$directory" -type f | sort -V)
```
