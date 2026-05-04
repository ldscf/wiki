---
source_title: Greenplum
categories:
- DB
- Develop
- Greenplum
last_modified: '2024-12-31T02:25:40Z'
---
Greenplum DB 号称是世界上第一个开源的大规模并行数据库，基于 PostgreSQL 构建。

Greenplum 在 PostgreSQL 的基础上采用MPP架构（Massive Parallel Processing，海量并行处理）构建，具有强大的大规模数据分析任务处理能力，包括数据水平分布、并行查询执行、专业优化器、线性扩展能力、多态存储、资源管理、高可用、高速数据加载等。提供 PD 级别数据量的强大和快速分析能力，特别是面向大数据方面的分析能力，支持大数据的超高性能分析查询。

Greenplum 由总部位于加利福尼亚州圣马特奥的一家同名公司于 2005 年创建，期间历经了多次交易，最后成为了 VMware 旗下产品。2023 年博通收购了 VMware，然后便将 VMware 产品全面改为订阅制，并对 VMware 进行了多次大规模裁员，猜测Greenplum 突然归档源代码仓库也是受此影响。
1. 2005年，数据库公司 Greenplum 成立
1. 2015年：Greenplum数据库从商业版正式向开源转变
1. 2023年：推出基于开源 PostgreSQL 的 AI 分析平台 Greenplum 7
1. 2023年11月：博通收购 VMware，GreenPlum 归入博通旗下。
1. 2024年：博通启动全球性裁员行动，Greenplum 商业化团队退出中国市场。Greenplum GitHub 仓库进入存档状态
