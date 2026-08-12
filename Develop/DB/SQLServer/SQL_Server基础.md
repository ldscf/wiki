---
source_title: SQL Server基础
categories:
- DB
- Develop
- SQLServer
last_modified: '2023-05-18T11:51:17Z'
---
环境：SQL Server 2012

### SQL Server Management Studiol
- Database Engine
- Analysis Services
- Reporting Services
- Integration Services

### Integration Services(SSIS)
- Business Intelligence 开发工具集成自微软 SQL Server Data Tools(SSDT)
- 可能需要同时提供 Visual Studio 2010 的安装包来安装 Visual Studio 2010 Shell

#### 开发

SQL Server Data Tools 默认配置

##### 数据流
- 建立数据库（目标/源：SQL Server， Oracle, Excel; Flat File）

###### 连接管理器配置
- 服务器名：是 DB 所在机器名称
- 选择一个数据库

##### 控制流

### 远程连接
- 需要使用 SQL Server 验证（数据库右键 --> 属性 --> 安全性）
- SSMS --> 安全性 --> 登录名，sa: 常规（修改密码），状态（登录-已启用

### Q/A

#### SSIS 无法连接服务器
- 以管理员权限运行 SQL Server Management Studio

#### SSIS 无法在unicode和非unicode字符串数据类型之间转换
