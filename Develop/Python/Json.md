---
source_title: Json
categories:
- Develop
- Python
last_modified: '2022-12-27T09:54:10Z'
---
Json 是一种数据传输格式。

Json 是根据某种约定格式编写的纯字符串，不具备任何数据结构的特征。要求必须且只能使用双引号作为key或者值的边界符号，不能使用单引号，而且"key"必须使用边界符（双引号）。

### dict <--> json
- loads()：将json数据转化成dict数据
- dumps()：将dict数据转化成json数据
- load()：读取json文件数据，转成dict数据
- dump()：将dict数据转化成json数据后写入json文件

### Example
```
 import json
```
 
```
 d1 = {"Apple": 12, "Pear": 0} 
 j1 = json.dumps(d1)
 #### '{"Apple": 12, "Pear": 0}'
```
 
```
 d2 = json.loads(j1)
 #### {'Apple': 12, 'Pear': 0}
```
