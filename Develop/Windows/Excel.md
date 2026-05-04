---
source_title: Excel
categories:
- Windows
last_modified: '2024-04-18T05:25:02Z'
---
1970 年，个人计算机刚刚兴起的时代， 出现了 VisiCalc —— 第一个个人计算机上的电子表格软件，VisiCalc 是 Visual 和 Calculation 的简称。VisiCalc 用数字表示“行(Row)”、用字母表示“列(Column)”，同时还定义了 单元格(Cell)、公式(Formula)、函数(Function)等在现在的 Excel 中依然耳熟能详的概念。

1982 年，VisiCalc 的前雇员去了 Lotus Software，在 IBM 计算机平台上推出了 Lotus 1-2-3。Lotus 1-2-3 垄断了 1980-1990 年间的表格应用市场，直到 1993 年 Windows 平台 Excel 第 5 版的推出。Lotus Software 在 1995 年被 IBM 以 35 亿美元收购。

### vlookup
1. 比较表中区域选择，第一列必须是比较列
1. $限定拷贝函数时，指定行列不发生变化
1. 一般需要指定精确比较，FALSE
1. IFERROR 处理 N/A 结果
- info

| Inx | No | Name | Value |
| 1 | 1001 | A |
| 2 | 1002 | B |
| 3 | 1005 | E |
| 4 | 1003 | C |
| 5 | 1004 | D |
- val

| No | Name | Value | Memo |
| 1001 | 87 |
| 1002 | 90 |
| 1005 | 72 |
| 1003 | 50 | e |
在 val 表中 Name 列，写如下函数：
```
 =VLOOKUP(A2, info!$B$2:$C$6, 2, FALSE)
```

| No | Name | Value | Memo |
| 1001 | A | 87 |
| 1002 | B | 90 |
| 1005 | E | 72 |
| 1003 | C | 50 | e |
在 info 表中 Value 列，写如下函数： 
```
 =IFERROR(VLOOKUP(B6, val!A6:D9, 3, FALSE), "")
```

| Inx | No | Name | Value |
| 1 | 1001 | A | 87 |
| 2 | 1002 | B | 90 |
| 3 | 1005 | E | 72 |
| 4 | 1003 | C | 50 |
| 5 | 1004 | D |
