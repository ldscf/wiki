---
source_title: Pandas json
categories:
- Develop
- Pandas
- Python
last_modified: '2022-12-29T07:36:45Z'
---
通常情况下，我们使用的都是 pandas 中的 to_json() 函数，可以通过设置 orient 参数来转换成为我们想要的 json 格式，orient 函数有以下几个参数：
- Series可选参数为："index"（默认）, "split", "records"
- DataFrame可选参数："columns"(默认)，split"，"records"， "index"，"values"

---

### data -> dataframe

#### data

area_proc.csv

| area_id | area_name | prod_type | prod_name | num | unit | total |
|:---|:---|:---|:---|:---|:---|:---|
| 10 | PEK | P1 | Apple | 12 | 5.2 | 62.4 |
| 10 | PEK | P2 | Pear | 20 | 3.3 | 66 |
| 11 | SHA | P1 | Apple | 8 | 4.5 | 36 |
| 11 | SHA | P2 | Pear | 15 | 2.5 | 37.5 |

#### dataframe
```
 ...
 df1 = pd.read_csv(u'area_proc.csv')
    area_id area_name prod_type prod_name  num  unit  total
 0       10       PEK        P1     Apple   12   5.2   62.4
 1       10       PEK        P2      Pear   20   3.3   66.0
 2       11       SHA        P1     Apple    8   4.5   36.0
 3       11       SHA        P2      Pear   15   2.5   37.5
```

### dataframe -> json

dataframe.to_json(orient="?", force_ascii=False)

?--> columns, split, records, index, values

#### dataframe -> json : columns

df1.to_json(orient="columns",force_ascii=False)

'{"area_id":{"0":10,"1":10,"2":11,"3":11},"area_name":{"0":"PEK","1":"PEK","2":"SHA","3":"SHA"},"prod_type":{"0":"P1","1":"P2","2":"P1","3":"P2"},"prod_name":{"0":"Apple","1":"Pear","2":"Apple","3":"Pear"},"num":{"0":12,"1":20,"2":8,"3":15},"unit":{"0":5.2,"1":3.3,"2":4.5,"3":2.5},"total":{"0":62.4,"1":66.0,"2":36.0,"3":37.5}}'

#### dataframe -> json : split

df1.to_json(orient="split",force_ascii=False)

'{"columns":["area_id","area_name","prod_type","prod_name","num","unit","total"],"index":[0,1,2,3],"data":[[10,"PEK","P1","Apple",12,5.2,62.4],[10,"PEK","P2","Pear",20,3.3,66.0],[11,"SHA","P1","Apple",8,4.5,36.0],[11,"SHA","P2","Pear",15,2.5,37.5]]}'

#### dataframe -> json : records

df1.to_json(orient="records",force_ascii=False)

'[{"area_id":10,"area_name":"PEK","prod_type":"P1","prod_name":"Apple","num":12,"unit":5.2,"total":62.4},{"area_id":10,"area_name":"PEK","prod_type":"P2","prod_name":"Pear","num":20,"unit":3.3,"total":66.0},{"area_id":11,"area_name":"SHA","prod_type":"P1","prod_name":"Apple","num":8,"unit":4.5,"total":36.0},{"area_id":11,"area_name":"SHA","prod_type":"P2","prod_name":"Pear","num":15,"unit":2.5,"total":37.5}]'

#### dataframe -> json : index

df1.to_json(orient="index",force_ascii=False)

'{"0":{"area_id":10,"area_name":"PEK","prod_type":"P1","prod_name":"Apple","num":12,"unit":5.2,"total":62.4},"1":{"area_id":10,"area_name":"PEK","prod_type":"P2","prod_name":"Pear","num":20,"unit":3.3,"total":66.0},"2":{"area_id":11,"area_name":"SHA","prod_type":"P1","prod_name":"Apple","num":8,"unit":4.5,"total":36.0},"3":{"area_id":11,"area_name":"SHA","prod_type":"P2","prod_name":"Pear","num":15,"unit":2.5,"total":37.5}}'

#### dataframe -> json : values

df1.to_json(orient="values",force_ascii=False)

'[[10,"PEK","P1","Apple",12,5.2,62.4],[10,"PEK","P2","Pear",20,3.3,66.0],[11,"SHA","P1","Apple",8,4.5,36.0],[11,"SHA","P2","Pear",15,2.5,37.5]]'

---

### json -> dataframe
