---
source_title: Sed
categories:
- Develop
- Linux
- Shell
last_modified: '2024-12-10T07:11:51Z'
---
sed 是 stream editor 的缩写，中文称之为“流编辑器”。

sed 命令是一个面向行处理的工具，它以“行”为处理单位，针对每一行进行处理，处理后的结果会输出到标准输出（STDOUT）。主要用来自动编辑文件、简化对文件的反复操作、编写转换程序等。

### **参数说明**
- -e 或 --expression= 以选项中指定的 script 来处理输入的文本文件。
- -f 或 --file= 以选项中指定的 script 文件来处理输入的文本文件。
- -h 或 --help 显示帮助。
- -n 或 --quiet 或 --silent 仅显示 script 处理后的结果。
- -V 或 --version 显示版本信息。

### **动作说明**
- a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
- c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行
- d ：删除，d 后面通常为空
- i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)
- p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起使用
- s ：替换，s 可以搭配正则表达式

### 删除

#### 删除行
```
 sed '1d'          # 删除第一行
 sed '1,3d'        # 删除第一、二、三行
 sed '1d;3d'       # 删除第一、三行
```

#### 删除空行
```
 sed '/^$/d'       # 删除空行
 sed '/^ *$/d'     # 删除含有空格的空行
```

#### 删除空格
```
 sed -e 's/[ ]*$//g'   # 删除行尾空格
 sed 's/^[ \t]*//g'?   # 删除行首空格
```

### 显示某一行
```
 sed -n '1,3p;5p'           # 输出1,2,3,5
 sed -n '1639924,1p'    # 输出第1639924行
```

### 替换

| OP | CMD | Explain |
|:---|:---|:---|
| + |
| AA -> BB | sed "s/AA/BB/g" | 末尾加 g 全部替换,否则只替换每行的第一个 |
| AA -> BB | sed '1,$s/AA/BB/g' | 替换第 1 行到最后一行 |
| AA -> BB | sed -i "s/AA/BB/g" FILE | 原文件替换 |
| 每行首加 AA | sed 's/^/AA&/g' |
| 每行尾加 AA | sed 's/$/&AA/g' |

## 以下参考

monitor

df -m |sed '1d'|awk '{print $2,$3,$4,$5,$6,$7,$8}'|sed '/^ *$/d'|sed -e 's/[ ]*$//g'|sed 's/ /-/g'

vmstat 1 2 |sed '1,3d'|awk '{print $1,$2,$3,$4,$5,$6,$7,$8,$9,$10,$11,$12,$13,$14,$15,$16,$17,$18}'|sed 's/ /;/g'

#多行合并成一行
1. 采用awk

    awk BEGIN{RS=EOF}'{gsub(/\n/," ");print}' file

    说明：awk默认将记录分隔符（record separator即RS）设置为\n，此行代码将RS设置为EOF（文件结束），也就是把文件视为一个记录，然后通过gsub函数将\n替换成空格，最后输出。
2. 采用sed

    sed ':a; N;s/\n/ /; t a; ' file

    说明：sed默认只按行处理，N可以让其读入下一行，再对\n进行替换，这样就可以将两行并做一行。但是怎么将所有行并作一行呢？可以采用sed的跳转功能。:a 在代码开始处设置一个标记a，在代码执行到结尾处时利用跳转命令t a重新跳转到标号a处，重新执行代码，这样就可以递归的将所有行合并成一行。
3. cat file | xargs

    xargs reads argu-

    ments from the standard input, delimited by blanks (which can be pro-

    tected with double or single quotes or a backslash) or newlines

将file中的内容作为参数传给X程序

    如果用echo作为X程序，则命令为：

    cat file | xargs echo

    此命令和cat file | xargs 行为一致，因为xargs的默认行为就是打印输出。

#计算一列数字

用awk命令计算文件中某一列的总和：

awk 'BEGIN{sum=0}{sum+=$1}END{print sum}' data.txt

比较完整的一个例子：

awk -F ',' 'BEGIN{sum=0 ;count=0}{if ($(NF-11) == 2 && $NF == 0 && $3 == "1.6.1_1_1") {sum +=$5; count++;} } END {print "sum="sum" count="count " avg="sum/count}'

说明：

BEGIN{sum=0 ;count=0} 初始化计数器；

END {print "sum="sum" count="count " avg="sum/count} 打印汇总，计数器 和均值；

if ($(NF-11) == 2 && $NF == 0 && $3 == "1.6.1_1_1") {sum +=$5; count++;} 判断倒数第11个字段，判断倒数第一个字段，判断第三个字段（字符串） 第五个字段汇总累加，计数器累加
