---
source_title: CSV
categories:
- DB
- Develop
last_modified: '2022-12-29T08:08:25Z'
---
CSV 的一种标准格式及仕样。

### CSV

一种合规的 csv 格式如下：
- 全部字段都可以使用双引号标识，如果字段里面有双引号，使用两个双引号
- 数值型字段、日期型字段可以不使用双引号
- 字符型字段，如果没有(','、回车、换行、双引号），也可以不使用双引号

#### test_cr.csv
```
 ky,val,ct,memo
 1001,Hello,2021-1-1,Test
 0002,Hi,2021-1-2 1:00,test1
 1002,"Hello, World!",2021-1-3 10:00,test2
 1003,"Hi, ada""'
 return",2021-1-4 15:00,Test message'.
 1004,"Hello, BI.",2021-1-10,
```

#### area_proc.csv
```
 area_id,area_name,prod_type,prod_name,num,unit,total
 10,PEK,P1,Apple,12,5.2,62.4
 10,TJ,P1,Pear,20,3.3,66
 10,PEK,P2,Fish,32,20,640
 11,SHA,P1,Apple,8,4.5,36
 11,SHA,P1,Pear,15,2.5,37.5
```
