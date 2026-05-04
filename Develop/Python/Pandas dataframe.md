---
source_title: Pandas dataframe
categories:
- Develop
- Pandas
- Python
last_modified: '2024-07-19T09:01:01Z'
---
![Pandas_DataStructure.png](https://mwbbs.eu.org/wiki/images/4/4d/Pandas_DataStructure.png)
```
　  　 DataFrame 是一个表格型的数据结构，它含有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔型值）。DataFrame 既有行索引也有列索引，它可以被看做由 Series 组成的字典（共同用一个索引）。
```

### pandas dataframe conversion

### dict

#### dict -> dataframe
```
 d1 = {"columns":["Apple","Pear"],"data":[[12,0], [8,7], [1, 9]]}
 df2 = pd.DataFrame(d1['data'])
    Apple  Pear
 0     12     0
 1      8     7
 2      1     9
```
 
```
 df2.columns=d1['columns']
 Index(['Apple', 'Pear'], dtype='object')
```

#### dataframe -> dict

Syntax: DataFrame.to_dict(orient=’dict’, into=)

Parameters:
- orient: String value, (‘dict’, ‘list’, ‘series’, ‘split’, ‘records’, ‘index’) Defines which dtype to convert Columns(series into). For example, ‘list’ would return a dictionary of lists with Key=Column name and Value=List (Converted series).
- into: class, can pass an actual class or instance. For example in case of defaultdict instance of class can be passed. Default value of this parameter is dict.

#### Example
```
 ...
 df2.to_dict('split')
 {'index': [0, 1, 2], 'columns': ['Apple', 'Pear'], 'data': [[12, 0], [8, 7], [1, 9]]}
```
 
```
 df2.to_dict('records')
 [{'Apple': 12, 'Pear': 0}, {'Apple': 8, 'Pear': 7}, {'Apple': 1, 'Pear': 9}]
```

### list

#### list -> dataframe
```
　　See also: dict -> dataframe
```

#### dataframe -> list
```
 a1 = df1.values    # values方法将dataframe转为numpy.ndarray
 l1 = a1.tolist()
 l1[0]              # get frist value
 usys.utime(l1[0][7].value/10**9) * P.S. 日期型的字段转换后格式：Timestamp('2017-04-13 13:48:32')，pandas._libs.tslibs.timestamps.Timestamp. 可以使用 usys.utime(l1[0][7].value/10**9) 转换。
```

### CSV

#### read
```
 # 默认: sep=',', quotechar='"', header=None(首行=0)
 df1 = pandas.read_csv('test.csv', sep=',', quotechar='"', header=0)
 # names 指定标题列(列不足，以末尾对齐)，优先于 header 指定
 df2 = pandas.read_csv('test.csv', header=None, names=['c1', 'c2'])
 # index_col=[列编号|列名](索引列前置), 默认 0 开始的虚拟列索引
 df3 = pandas.read_csv('test.csv', header=0, index_col=[0, 2])
 # 字符集 utf8, gb18030
 encoding = 'utf8'
 # 跳过行
 skiprows = range(2)               # 以 0 始计
 skiprows = 2                      # 仅第三行
 skiprows = lambda x: x % 2 != 0   # 0=偶数行, 1=奇数行
 skipfooter = 1                    # 去掉最后一行(引擎 'c' 无效)
```
 
```
 *<small># df3 = pandas.read_csv('test.csv', header=0, skipfooter=1, index_col=[])
 # <stdin>:1: ParserWarning: Falling back to the 'python' engine because the 'c' engine does not support skipfooter; you can avoid this warning by specifying engine='python'*
```
 
```
 # 处理大量数据，可以指定引擎
 engine='c' | 'python'
 # 跳过空行, default true
 skip_blank_lines = False
 # 最大读取
 nrows = 1000
 # 默认空值
 ['-1.#IND', '1.#QNAN', '1.#IND', '-1.#QNAN', '#N/A N/A', '#N/A', 'N/A', 'n/a', 'NA', '#NA', 'NULL', 'null', 'NaN', '-NaN', 'nan', '-nan', '']
 keep_default_na = False            # 不使用默认空值
 na_values=["NA", "0"]              # 自定义空
```
