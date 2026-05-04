---
source_title: Greenplum Config
categories:
- DB
- Develop
- Greenplum
last_modified: '2022-12-29T08:15:05Z'
---
Greenplum 在 PostgreSQL 的基础上采用MPP架构（Massive Parallel Processing，海量并行处理）构建,具有强大的大规模数据分析任务处理能力，包括数据水平分布、并行查询执行、专业优化器、线性扩展能力、多态存储、资源管理、高可用、高速数据加载等。

### Greenplum Config

#### Greenplum数据库时区
- gpconfig -c TimeZone -v 'Asia/Shanghai'
- gpconfig -s TimeZone

#### 日志记录内容
- alter system set log_statement = ddl;
- gpconfig -c log_statement -v none -m ddl
- gpconfig -s log_statement;
- show log_statement;
  - none, ddl, mod, all
  - *ddl记录所有数据定义命令，比如CREATE,ALTER,和DROP语句。
  - *mod记录所有ddl语句,加上数据修改语句INSERT,UPDATE等。
  - *all记录所有执行的语句，将此配置设置为all可跟踪整个数据库执行的SQL语句

#### gp_vmem_protect_limit
- 控制了每个segment数据库为所有运行的查询分配的内存总量。查询需要的内存不超过此值
- gpconfig -c gp_vmem_protect_limit -v 8192
- gpconfig -s gp_vmem_protect_limit

#### resource_select_only
- 默认情况下受资源队列限制的，只有SELECT, SELECT INTO, CREATE TABLE AS SELECT, 和 DECLARE CURSOR 受限。如果配置参数 resource_select_only = off，则 INSERT, UPDATE, DELETE 语句也会受限。
- gpconfig -s resource_select_only

#### gp_workfile_limit_files_per_query
- SQL查询分配的内存不足，Greenplum数据库会创建溢出文件（工作文件）。在默认情况下，一个SQL查询最多可以创建 100000 个工作文件
- gpconfig -s gp_workfile_limit_files_per_query

#### effective_cache_size
- PostgreSQL的优化器有多少内存可以被用来缓存数据，以及帮助决定是否应该使用索引。这个数值越大，优化器使用索引的可能性也越大。因此这个数值应该设置成shared_buffers加上可用操作系统缓存两者的总量。通常这个数值会超过系统内存总量的50%以上。
- gpconfig -s effective_cache_size

#### work_mem
- (物理内存的2%-4%), segment用作sort,hash操作的内存大小
- 当PostgreSQL对大表进行排序时，数据库会按照此参数指定大小进行分片排序，将中间结果存放在临时文件中，这些中间结果的临时文件最终会再次合并排序，所以增加此参数可以减少临时文件个数进而提升排序效率。当然如果设置过大，会导致swap的发生，所以设置此参数时仍需谨慎。
- gpconfig -c work_mem  -v 32MB
- gpconfig -s work_mem

#### maintenance_work_mem
- CREATE INDEX, VACUUM等时用到,segment用于VACUUM,CREATE INDEX等操作的内存大小，缺省是16兆字节(16MB)。因为在一个数据库会话里， 任意时刻只有一个这样的操作可以执行，并且一个数据库安装通常不会有太多这样的工作并发执行， 把这个数值设置得比work_mem更大是安全的。 更大的设置可以改进清理和恢复数据库转储的速度。
- gpconfig -c maintenance_work_mem -v 64MB
- gpconfig -s maintenance_work_mem

#### 参数生效

gpstop -u

#### Other
1. gpconfig -s max_connections
1. gpconfig -s max_statement_mem
1. gpconfig -s statement_mem
1. gpconfig -s gp_resqueue_priority_cpucores_per_segment
1. gpconfig -s TimeZone
1. show log_statement;      --记录用户登陆数据库后的各种操作，默认all
1. show logging_collector;  --是否开启日志收集，默认off
1. show log_destination;    --日志记录类型，默认是stderr，只记录错误输出
1. show log_connections;    --用户session登陆时是否写入日志，默认off
1. show log_disconnections; --用户session退出时是否写入日志，默认off
1. show log_rotation_age;   --保留单个文件的最大时长,默认是1d,也有1h,1min,1s
1. show log_rotation_size;  --保留单个文件的最大尺寸，默认是10MB
