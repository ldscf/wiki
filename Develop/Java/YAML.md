---
source_title: YAML
categories:
- Develop
- Java
last_modified: '2024-06-18T06:55:38Z'
---
YAML 是 "YAML Ain't a Markup Language"（YAML 不是一种标记语言）的递归缩写，或者是："Yet Another Markup Language"（仍是一种标记语言）。

YAML 可以简单表达清单、散列表，标量等数据形态。它使用空白符号缩进和大量依赖外观的特色，特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲（例如：许多电子邮件标题格式和YAML非常接近）。

### 语法
- 大小写敏感
- 使用缩进表示层级关系
- 缩进只允许空格
- 相同层级的元素左对齐
- # 表示注释

### 数据类型

#### 对象

键值对的集合，又称为映射(mapping)/ 哈希(hashes) / 字典(dictionary)
```
 key1: 
   key11: value1
   key12: value2
 -> key1: {key11: value1, key12: value2}
```
 
```
 key1: 
   key11:
     key111: value
 -> key1: {key11: {key111: value}}
```
- 数组：一组按次序排列的值，又称为序列(sequence) / 列表(list)
```
 key:
   - value1
   - value2
```
 
```
 -> key: [value1, value2]
```

复杂对象
```
 ?  
   - key1
   - key2
 :
   - value1
   - value2
 -> [key1, key2]: [value1, value2]
```

组合对象
```
 obj:
   - id: 101
     name: A1
     price: 200
   - id: 102
     name: C2
     price: 100
 -> obj: [{id: 101, name: A1, price: 200}, {id: 102, name: C2, price: 100}]
```
- 纯量(scalars)：单个的、不可再分的值

### 引用

& 用来建立锚点，<< 表示合并到当前数据，* 用来引用锚点。
```
 db1: &db1
   adapter:  postgres
   host:     localhost
 dev:
   database: myapp_development
   <<: *db1
 test:
   database: myapp_test
   <<: *db1
```
