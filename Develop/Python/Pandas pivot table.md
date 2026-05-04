---
source_title: Pandas pivot table
categories:
- Develop
- Pandas
- Python
last_modified: '2022-12-29T07:39:08Z'
---
The summarization can be upon a variety of statistical concepts like sums, averages, etc. for designing these pivot tables from a pandas perspective the pivot_table () method in pandas library can be used. This is an effective method for drafting these pivot tables in pandas.

#### 函数与格式

pivot_table(data, index, columns, values/aggfunc, margins)

![Python pivot table.png](https://mwbbs.eu.org/wiki/images/8/83/Python pivot table.png)
- index, columns, values/aggfunc, margins
- 蓝、黄、绿、紫/红

#### aggfunc
- 求和：sum
- 求均值：mean
- 求个数：size
- 极值：max, min

#### margins
- 按列分类求小计及行总计，无行分类小计。

---

#### Cross Table(single value)
```
 import pandas as pd
 df1 = pd.read_csv('area_proc_en.csv')
```
 
```
    area_id area_name prod_type prod_name  num  unit  total
 0       10       PEK        P1     Apple   12   5.2   62.4
 1       10        TJ        P1      Pear   20   3.3   66.0
 2       10       PEK        P2      Fish   32  20.0  640.0
 3       11       SHA        P1     Apple    8   4.5   36.0
 4       11       SHA        P1      Pear   15   2.5   37.5
```
 
 
```
 # cross table
 pt1 = pd.pivot_table( df1,
  index      =['area_id', 'area_name', 'prod_type'],
  columns    =['prod_name'], 
  values     ='num', 
  aggfunc    ={'num':'sum'}, 
  fill_value = 0
 )
```
 
```
 prod_name                    Apple  Fish  Pear
 area_id area_name prod_type                   
 10      PEK       P1            12     0     0
                   P2             0    32     0
         TJ        P1             0     0    20
 11      SHA       P1             8     0    15
```
 
 
```
 # Add Total, sub Total
 pt2 = pd.concat([
     d.append(d.sum().rename((k, '小计')))
     for k, d in pt1.groupby(level=0)
 ]).append(pt1.sum().rename(('全国', '合计')))
```
 
```
 prod_name      Apple  Fish  Pear
 (10, PEK, P1)     12     0     0
 (10, PEK, P2)      0    32     0
 (10, TJ, P1)       0     0    20
 (10, 小计)          12    32    20
 (11, SHA, P1)      8     0    15
 (11, 小计)           8     0    15
 (全国, 合计)          20    32    35
```
 
```
 pt3.to_json(orient="split",force_ascii=False)
```
 
```
 '{"columns":[["num","Apple"],["num","Fish"],["num","Pear"],["num","All"],["total","Apple"],["total","Fish"],["total", "Pear"],["total","All"],["unit","Apple"],["unit","Fish"],["unit","Pear"],["unit","All"]],"index":[[10,"PEK","P1"],[10 ,"PEK","P2"],[10,"TJ","P1"],[11,"SHA","P1"]],"data":[[12,0,0,12,62.4,0,0.0,62.4,5.2,0,0.0,5.2],[0,32,0,32,0.0,640,0.0 ,640.0,0.0,20,0.0,20.0],[0,0,20,20,0.0,0,66.0,66.0,0.0,0,3.3,3.3],[8,0,15,23,36.0,0,37.5,73.5,4.5,0,2.5,3.5]]}'
```

#### Cross Table(multi value)
```
 pt2 = pd.pivot_table( df1,
  index      =['area_id', 'area_name', 'prod_type'],
  columns    =['prod_name'], 
  values     =['num', 'unit', 'total'], 
  aggfunc    ={'num':'sum', 'unit':'mean', 'total':'sum'}, 
  fill_value = 0
 )
                              num           total             unit          
 prod_name                   Apple Fish Pear Apple Fish  Pear Apple Fish Pear
 area_id area_name prod_type                                                 
 10      PEK       P1           12    0    0  62.4    0   0.0   5.2    0  0.0
                   P2            0   32    0   0.0  640   0.0   0.0   20  0.0
         TJ        P1            0    0   20   0.0    0  66.0   0.0    0  3.3
 11      SHA       P1            8    0   15  36.0    0  37.5   4.5    0  2.5
```
 
 
```
 # Add Total, sub Total
 pt3 = pd.concat([
     d.append(d.sum().rename((k, '小计')))
     for k, d in pt3.groupby(level=0)
 ]).append(pt3.sum().rename(('全国', '合计')))
```
 
```
                 num                   total                       unit                 
 prod_name     Apple  Fish  Pear   All Apple   Fish   Pear    All Apple  Fish Pear   All
 (10, PEK, P1)  12.0   0.0   0.0  12.0  62.4    0.0    0.0   62.4   5.2   0.0  0.0   5.2
 (10, PEK, P2)   0.0  32.0   0.0  32.0   0.0  640.0    0.0  640.0   0.0  20.0  0.0  20.0
 (10, TJ, P1)    0.0   0.0  20.0  20.0   0.0    0.0   66.0   66.0   0.0   0.0  3.3   3.3
 (10, 小计)       12.0  32.0  20.0  64.0  62.4  640.0   66.0  768.4   5.2  20.0  3.3  28.5
 (11, SHA, P1)   8.0   0.0  15.0  23.0  36.0    0.0   37.5   73.5   4.5   0.0  2.5   3.5
 (11, 小计)        8.0   0.0  15.0  23.0  36.0    0.0   37.5   73.5   4.5   0.0  2.5   3.5
 (全国, 合计)       20.0  32.0  35.0  87.0  98.4  640.0  103.5  841.9   9.7  20.0  5.8  32.0
```
 
 
```
 pt3.to_json(orient="split",force_ascii=False)
```
 
```
 '{"columns":[["num","Apple"],["num","Fish"],["num","Pear"],["num","All"],["total","Apple"],["total","Fish"],["total", "Pear"],["total","All"],["unit","Apple"],["unit","Fish"],["unit","Pear"],["unit","All"]],"index":[[10,"PEK","P1"],[10 ,"PEK","P2"],[10,"TJ","P1"],[11,"SHA","P1"]],"data":[[12,0,0,12,62.4,0,0.0,62.4,5.2,0,0.0,5.2],[0,32,0,32,0.0,640,0.0,640.0,0.0,20,0.0,20.0],[0,0,20,20,0.0,0,66.0,66.0,0.0,0,3.3,3.3],[8,0,15,23,36.0,0,37.5,73.5,4.5,0,2.5,3.5]]}'
```

#### Cross Table(margins)
```
 pt2 = pd.pivot_table( df1,
  index      =['area_id', 'area_name', 'prod_type'],
  columns    =['prod_name'], 
  values     =['num', 'unit', 'total'], 
  aggfunc    ={'num':'sum', 'unit':'mean', 'total':'sum'}, 
  margins    = True,
  fill_value = 0
 )
```
 
```
                               num               total                     unit                
 prod_name                   Apple Fish Pear All Apple Fish   Pear    All Apple Fish Pear   All
 area_id area_name prod_type                                                                   
 10      PEK       P1           12    0    0  12  62.4    0    0.0   62.4  5.20    0  0.0   5.2
                   P2            0   32    0  32   0.0  640    0.0  640.0  0.00   20  0.0  20.0
         TJ        P1            0    0   20  20   0.0    0   66.0   66.0  0.00    0  3.3   3.3
 11      SHA       P1            8    0   15  23  36.0    0   37.5   73.5  4.50    0  2.5   3.5
 All                            20   32   35  87  98.4  640  103.5  841.9  4.85   20  2.9   7.1
```
 
 
```
 pt2.to_json(orient="split",force_ascii=False)
```
 
```
 '{"columns":[["num","Apple"],["num","Fish"],["num","Pear"],["num","All"],["total","Apple"],["total","Fish"],["total", "Pear"],["total","All"],["unit","Apple"],["unit","Fish"],["unit","Pear"],["unit","All"]],"index":[[10,"PEK","P1"],[10 ,"PEK","P2"],[10,"TJ","P1"],[11,"SHA","P1"]],"data":[[12,0,0,12,62.4,0,0.0,62.4,5.2,0,0.0,5.2],[0,32,0,32,0.0,640,0.0 ,640.0,0.0,20,0.0,20.0],[0,0,20,20,0.0,0,66.0,66.0,0.0,0,3.3,3.3],[8,0,15,23,36.0,0,37.5,73.5,4.5,0,2.5,3.5]]}'
```
