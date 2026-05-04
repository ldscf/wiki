---
source_title: Pandas
categories:
- Develop
- Pandas
- Python
last_modified: '2024-07-15T07:07:16Z'
---
```
　  　 Pandas 是一个开放源码、BSD 许可的库，提供高性能、易于使用的数据结构和数据分析工具。Pandas 库基于 NumPy 库（提供高性能的矩阵运算）开发而来，可以与之配合使用。Pandas 提供了两种数据结构，分别是 Series（一维数组结构）与 DataFrame（二维数组结构）
```

### Pandas 基础

#### Series
- Series 类似表格中的一个列（column），类似于一维数组，可以保存任何数据类型。
- Series 由索引（index）和列组成，函数如下：
- pandas.Series( data, index, dtype, name, copy)

#### DataFrame
- DataFrame 是一个表格型的数据结构，它含有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔型值）。
- DataFrame 既有行索引也有列索引，它可以被看做由 Series 组成的字典（共同用一个索引）。
- pandas.DataFrame( data, index, columns, dtype, copy)

##### DataFrame
- get column
  - df2.columns
  - df2.columns[0]
- 删除 Apple 列值为空的行
  - df1.dropna(subset=['Apple'])

##### List
```
 --> DataFrame
 list1 = ["a", "b", "c"]
 df1 = pd.DataFrame(list1, columns=["val"])
 <-- DataFrame
 list1 = df1['val'].tolist()
```
 
