---
source_title: Mermaid
categories:
- Develop
- Tools
last_modified: '2025-04-29T07:55:19Z'
---
![Mermaid.png](https://mwbbs.eu.org/wiki/images/6/66/Mermaid.png)

[Mermaid](https://mermaid.nodejs.cn/)是基于 JavaScript 的图表工具，可渲染 Markdown 启发的文本定义以动态创建和修改图表。荣获 2019 年 Javascript 开源奖之“最令人兴奋的技术应用”。

支持的图表类型：

| No | Type | Type Name | Type NameE | Describe |
|:---|:---|:---|:---|:---|
| + |
| 1 | flowchart | 流程图 | Flowcharts | 流程或过程中的步骤 |
| 2 | sequenceDiagram | 序列图 | Sequence Diagrams | 对象之间的交互 |
| 3 | gantt | 甘特图 | Gantt Charts | 项目管理和时间规划 |
| 4 | classDiagram | 类图 | Class Diagrams | 类及其关系 |
| 5 | 状态图 | State Diagrams | 描述系统状态及其转换 |
| 6 | pie | 饼图 | Pie Charts | 展示数据的比例关系 |
| 7 | 实体关系图 | Entity Relationship Diagrams, ERDs | 数据库的实体及其关系 |

### draw.io
```
 # v26.2.2
 调整图形 -> 插入 -> 高级 -> Mermaid
 # v26.2.15
 调整图形 -> 插入 -> Mermaid
```

### Sample

#### flowchart
 ```
flowchart TB
     B(Begin) --> I1{IF}
     I1 -- Y --> C1[Content1](#Content1)
     I1 -- N --> C2(Content2)
     C1 --> E((END))
     C2 --> E
 
     %% Style definitions
     style B  fill:#ccffcc,stroke:#333,stroke-width:2px
     style I1 fill:#ffeebb,stroke:#333,stroke-width:2px
     style C1 fill:#ddeeff,stroke:#333,stroke-width:2px
     style C2 fill:#ddeeff,stroke:#333,stroke-width:2px
     style E  fill:#ccffcc,stroke:#333,stroke-width:2px
```

#### gantt
 ```
gantt
    section Section
    Completed    :done,    des1, 2014-01-06,2014-01-08
    Active       :active,  des2, 2014-01-07, 3d
    Parallel 1   :         des3, after des1, 1d
    Parallel 2   :         des4, after des1, 1d
    Parallel 3   :         des5, after des3, 1d
    Parallel 4   :         des6, after des4, 1d
```

### Online
1. [NodeJS 旗下网站](https://mermaid-live.nodejs.cn/)、
1. [min2k(支持转为 draw.io)](https://www.min2k.com/tools/mermaid/)
1. [jyshare(支持转为 PNG)](https://www.jyshare.com/front-end/9729/)
