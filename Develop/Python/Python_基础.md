---
source_title: Python 基础
categories:
- Develop
- Python
last_modified: '2025-11-06T09:04:16Z'
---
本文档包括：基本语法、string、re

### Base

#### Install
```
 # pip upgrade
 python3 -m pip install --upgrade pip
```
 
```
 # 已安装包
 pip3 list
```
 
```
 # 安装本地文件
 Pip3 install numpy…whl
```

#### 虚拟环境
```
 python -m venv ${VENV_NAME}
 . ${VENV_NAME}/bin/activate         # 激活虚拟环境
 deactivate                          # 关闭虚拟环境（内部命令）
```

#### reload
```
 import importlib
 importlib.reload(usql_sys)
```

#### sys
```
 sys.argv 是命令行参数列表
 python test.py hello world 123
 sys.argv: ['test.py', 'hello', 'world', '123']
```

#### ENV
```
 # Oracle 客户端字符集、钱包目录
 os.environ["NLS_LANG"] = "AMERICAN_AMERICA.UTF8"
 os.environ["TNS_ADMIN"] = "/home/user/wallets/adb_wallet"
```

### Dev

#### 指定编码

变量中出现非ASCII字符，则需要指定处理之编码，如：utf-8
```
 #coding= 
 OR
 #!/usr/bin/python3
 #-*- coding: utf-8 -*-
```

#### 引入路径
- 同目录或系统
```
 import sys
 import os
 import ustr
```
- 当前文件下一级目录
```
 import udef.ustr as ustr
 # or from udef.ustr import to_num
```
- 当前文件同级目录
```
 sys.path.append("..")
 import udef.ustr as ustr
```
- 指定目录
```
 sys.path.append("/usr/include/udefpy3")
 import ustr
 # ustr 为 udefpy3 下文件
```

#### 建目录（多级，相当于 -p）
```
 if not os.path.exists(path_op):
```

     os.makedirs(path_op)

#### 判断文件是否存在
```
 os.path.exists(FN)
 - OR -
 os.path.isfile(FN)
 - OR -
 os.access(FN, os.F_OK|R_OK|W_OK|X_OK)
```

#### 当前路径
```
 os.chdir('./ppy')
 os.getcwd()
```

#### 动态执行命令
```
 TOPIC=b"l_serv_monitor_io"
 CMD="mqkc.topics[TOPIC]"
 mqtp = eval(CMD)
```

#### 编译

#### DEBUG
```
 python3 -m pdb mqlog_oc_mc3 n=10000 offset=0
```
 
```
 b umq.py:353  设置断点
 c 执行到下一个断点
```
 
```
 l 列出附近代码
```

#### 特殊字符

PA='\x04'

#### 三目

max = a if a>b else b

#### lamda 表达式
```
 uncol = str([x for x in re.split(" *, *", uncol) if x]).strip('[]')
 cols = re.sub('[\[\]\']', *, str([x for x in re.split(" *, *", cols) if x]))*
```

#### 文件

open() 是内置函数，它会返回一个文件对象，这个文件对象拥有 read、readline、readlines、write、close 等方法。
```
 open(filename, 'mode')
```
- r：表示文件只能读取
- w：表示文件只能写入
- a：表示打开文件，在原有内容的基础上追加内容，在末尾写入
- w+:表示可以对文件进行读写双重操作

以上为读 txt 格式，若读二进制格式，则加上 b，如：rb

方法

均可指定读入字符数
- read(): 读为字符串（在文本模式下）或字节对象（在二进制模式下）
- readline(): 读取整行
- readlines(): 读取所有行，返回列表
- write(str): 写入字符串

#### 时间格式
```
 import datetime
 # /var/log/apache2/access.log 中时间格式
 sd1 = '16/Dec/2024:09:15:25 +0800'
 d1 = datetime.datetime.strptime(sd1, '%d/%b/%Y:%H:%M:%S %z').strftime('%Y%m%d%H%M%S')
```

### 变量类型

#### string

##### split
```
 # 将字符串按分隔符切几次，切分的最后部分为剩余全部；如：n=1时，字符串为两部分
 v1 = "1010;msinfo;bidb01;10.10.137.10;/;32"
 key,type,name,ip,v1 = v1.split(';', 4)
 #也可以把拆分后的结果存到 list
 v1_list = v1.split(';')
 ## strip
 #移除字符串头尾指定的单字符，支持多个单字符。
 str.strip([chars])
 s=',hello, world.,!,'
 s.strip(',.!')                # 'hello, world'
 str(e).replace('\r', ' ').replace('\n', ' ') # delete \r\n
 ## 重复字符
 v1 = "%s," * 10
 ## 子字符个数
 s.count(',')
```

##### 字符串方法

str.upper()

str.lower()

str.capitalize()  

首个字母大写，其余小写

str.casefold() 

消除大小写类似于转为小写，但是更加彻底一些，因为它会移除字符串中的所有大小写变化形式。 例如，德语小写字母 'ß' 相当于 "ss"。 由于它已经是小写了，lower() 不会对 'ß' 做任何改变；而 casefold() 则会将其转换为 "ss"

str.center(width[, fillchar])

返回长度为 width 的字符串，原字符串在其正中。 使用指定的 fillchar 填充两边的空位（默认使用 ASCII 空格符）。 如果 width 小于等于 len(s) 则返回原字符串的副本

str.count(sub[, start[, end]])

返回子字符串 sub 在 [start, end] 范围内非重叠出现的次数。 可选参数 start 与 end 会被解读为切片表示法。

If sub is empty, returns the number of empty strings between characters which is the length of the string plus one

#### List
```
 v1 = ['abc', ' ', 'aa', ';', 'bb', ',', 'cc', ' ', '|', 'dd(xx).xxx', ' ', "12.12'", ' ', 'xxxx']
```
- 添加/删除元素
```
 v1.append('Apple') 
 del v1[0]
 del_last = v1.pop(-1)    # 删除最后一个值
 v1.remove('a')           # 删除第一个匹配值，若找不到报错
```
- 列表截取，list[parm1:parm2:parm3]
1. parm1：开始下标，可以为空，默认为0
1. parm2：结束下标，可以为空，默认为 list.size，但是不包括 list.size，左闭右开
1. param3：步长，默认为1
- 截取
  - v1[1]     # 第二个元素
  - v1[-2]    # 倒数第二个元素
  - v1[::-1]  # 逆序列表
  - v1[:-1]   # 除了最后一个，取全部
  - v1[2::-1] # 从第三个元素开始到结束的逆序
- 合并
```
 v2 = ['add1', 'add2', 123]
 v1 = [*v1, *v2]
```

#### Dict

Python dict(字典) 以 键值对 (key-value pair) 的形式存储数据：
* **可变的 (mutable)：** 添加、删除或修改字典中的键值对
* **无序的 (unordered)：** 字典中的键值对没有特定的顺序 (从 Python 3.7 开始，会保留插入顺序，但 CPython、Jython 或 IronPython 不确定)
* **键唯一 (unique keys)：** 字典中不能有两个相同的键
* **键不可变 (immutable keys)：** 键通常是字符串、数字或元组，但不能是列表或其他可变类型
* **值可以是任何类型 (any type values)：** 值可以是任何 Python 对象，包括数字、字符串、列表、元组、字典，甚至是函数

### 包

#### re

##### re.split 切分
```
 import re
 v1="abc aa;bb,cc | dd(xx).xxx 12.12' xxxx"
 >>> re.split(r' ', v1)
 ['abc', 'aa;bb,cc', *, '|', 'dd(xx).xxx', "12.12'\txxxx"]*
 >>> re.split(r'[\s]', v1) 
 ['abc', 'aa;bb,cc', *, '|', 'dd(xx).xxx', "12.12'", 'xxxx']*
 >>> re.split(r'[\s;,.()]', v1)
 ['abc', 'aa', 'bb', 'cc', *, '|', 'dd', 'xx', *, 'xxx', '12', "12'", 'xxxx']
 >>> re.split(r'bb,', v1)
 ['abc aa;', "cc | dd(xx).xxx 12.12'\txxxx"]
 >>> re.split(r'[,; ] *', v1)
 ['abc', 'aa', 'bb', 'cc', '', 'dd(xx).xxx', "12.12'", 'xxxx']
 >>> re.split(r'(,|;| |\|) *', v1)   # 带分隔符
 ['abc', ' ', 'aa', ';', 'bb', ',', 'cc', ' ', '', '|', 'dd(xx).xxx', ' ', "12.12'", ' ', 'xxxx']
 >>> 可以使用 [::2] 去掉分隔符
```

##### 与 str.split 比较，相同部分
```
 s1='a,b,c,d, ,,12.3'
 >>> re.split(',', s1)
 ['a', 'b', 'c', 'd', ' ', *, '12.3']*
 >>> s1.split(',')
 ['a', 'b', 'c', 'd', ' ', *, '12.3']*
 >>> s1.split('c,') 
 ['a,b,', 'd, ,,12.3']
```

string 可以指定分解的个数
```
 >>> s1.split(',',3)
 ['a', 'b', 'c', 'd, ,,12.3']
```

##### re.sub 替换
- .  = 匹配任意字符（除了换行符）
- *  = 匹配前一个字符0次或多次
- *? = 非贪婪匹配（惰性匹配）
```
 re.sub(' ', *, p_str1)*
 str1 = 'a, b , c ,d e, f1,g'
 re.sub(' *, *', ',', str1)
```
```
 s="select a,b,c,eee from tab where sfrom = 'abc'"
 re.sub('select.*?from', *, s)  *
 # " tab where sfrom = 'abc'"
```

惰性匹配
```
 #*,+,?等都是贪婪匹配，后面加?号使其变成惰性匹配
 cols = re.sub(':.*?,', ',', s1+',').strip(',') # s1=uuid:1|32|h,etl_id:2|8|r,...
```

分组匹配
```
 import re
 s = '110223199001011123'
 res = re.search('(?P\d{3})(?P\d{3})(?P\d{4})(?P\d{2}',s)
 print(res.groupdict())
 此分组取出结果为：
 {'province': '110', 'city': '223', 'born_year': '1990', 'bron_month': '01'}
```

##### re.compile 匹配

re.compile 是 Python 中用于编译正则表达式的函数，它返回一个正则表达式对象，该对象可以用于匹配操作。编译正则表达式可以提高匹配效率，特别是当同一个表达式需要多次匹配时。
```
 #解析 apache2 日志：
 # import re
 # line 为 /var/log/apache2/access.log 中一行
 log_pattern = re.compile(r'^(\S+) \S+ \S+ \[(.+?)\] \"(?:GET|POST|HEAD|PUT|DELETE|OPTIONS|TRACE) (.+?)(?:\?.*?)? HTTP/\d\.\d" (\d{3}) (\d+|\-) \".*?\" \"(.*?)\"$')
 match = log_pattern.match(line)
 client_ip = match.group(1)
 ...
```

##### Example

2025年11月6日 -> 20251106
```
 date_lst = re.search(r'(\d{4})年(\d{1,2})月(\d{1,2})日', date_str)
 year, month, day = date_lst.groups()
 # 更精确的控制
 re.search(r'(\d{1,4})年(0?[1-9]|1[0-2])月(0?[1-9]|[12][0-9]|3[01])日', date_str)
```

取{...}
```
 re.search('\{.*\}', s1)             # s1 = 'a1, {col1, col2}, a2'
```

替换超过五个数字
```
 re.sub(r'\d{5,}', '32000', str1)    # str1 = 'varchar2(65536)'
```

##### Error Info
```
 # except Exception as e:
 str(e).split('\n')[0]
 # raise
 raise ValueError('Error.')
```

### Tips

#### 迭代

### Error
- (pip)AttributeError: module 'lib' has no attribute 'X509_V_FLAG_CB_ISSUER_CHECK'

python/pip 和 pyOpenSSL 版本不对应
```
 apt remove python3-openssl -y
 apt autoremove -y
```
 
```
 # pip install pyOpenSSL
 apt install python3-openssl
```
 
```
 ## https://askubuntu.com/a/1433089/497392
 # apt remove python3-pip
 wget https://bootstrap.pypa.io/get-pip.py
 python3 get-pip.py
 pip3 install pyopenssl --upgrade
```
