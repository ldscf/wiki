---
source_title: DBeaver
categories:
- DB
- Develop
- OtherDB
last_modified: '2025-09-07T03:46:19Z'
---
[DBeaver](https://dbeaver.io) 是一款 开源的通用数据库管理工具，基于 Java + Eclipse RCP 开发。支持多种数据库（SQL 与 NoSQL），常用于数据库开发、调试和运维管理。

### Run DBeaver With a  Wallet

#### Wallet

wallet_*.zip 文件解压放入 

#### TNS

TNS name path: 

Network Alias: 可以选择 tnsnames.ora 中定义的连接

### Run DBeaver With a Datafuse jar
1. 数据库 -> 驱动管理器 -> 新建
1. 驱动名称（Driver Name）：Datafuse  

类名（Class Name）：com.yoyosys.df.sdk.jdbc.Driver  

URL 模板（URL Template）：jdbc:datafuse:url=http://{host}:{port}/{database}  

在 TAB: 库中，添加 jar 文件  

这样，在连接到数据库中，选择窗口中可以看到 Datafuse
