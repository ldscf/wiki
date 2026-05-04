---
source_title: Java 自定义方法 - CNF
categories:
- Develop
- Java
last_modified: '2024-10-30T08:07:52Z'
---
Java 自定义函数 - CNF

从配置文件中获取 item 定义 name1, name2,...，配置文件格式：
```
 item.name1=value
 item.name2=value
```

| No | Method | Explain | Example |
|:---|:---|:---|:---|
| + |
| 0 | CNF | 读配置文件，支持全路径，默认当前目录下 config 目录 | CNF cnf1 = new CNF("sys.cnf") |
| get | 获取指定 key 值或全部值 |
| put | 将指定 item 组值放入 MAP | cnf1.put(dbinfo, "db_" + sType) |

### GIT

https://github.com/ldscfe/devudefj2/blob/main/src/main/com/udf/CNF.java
