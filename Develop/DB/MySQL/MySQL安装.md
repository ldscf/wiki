---
source_title: MySQL安装
categories:
- DB
- Develop
- MySQL
last_modified: '2026-03-07T03:38:05Z'
---
MySQL 安装及配置

### MySQL Install
- [MySQL](https://downloads.mysql.com/archives/community/)

#### Ubuntu 22.04
- apt update
- apt install mysql-server
```
 # mysql --version
 mysql  Ver 8.0.32-0ubuntu0.22.04.2 for Linux on x86_64 ((Ubuntu))
```

#### 二进制免安装版
1. 将解压包移至：/usr/local/mysql

# 初始化数据库：./bin/mysqld --initialize --user=$(whoami) --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data  

会在当前目录建立 data 存放数据库文件，注意输出信息中的临时密码

# 启动：./bin/mysqld_safe --datadir=/usr/local/mysql/data

# 配置环境变量：echo 'export PATH="/usr/local/mysql/bin:$PATH"' >> ~/.zshrc  

source ~/.zshrc

#### DEB

https://cdn.mysql.com/archives/mysql-connector-java-8.0/mysql-connector-j_8.0.31-1ubuntu22.04_all.deb
```
 ## Install DEB package
 apt install gdebi
 # 安装了一堆东西，还要重启什么服务，直接 Cancel 了，下面安装 deb 包也正常。
 gdebi ???.deb
```

### Config

#### 远程监听
```
 # /etc/mysql/mysql.conf.d/mysqld.cnf
 bind-address= 0.0.0.0
```

#### datadir

数据文件存储路径(/var/lib/mysql)
- 新建数据存放的目录，移动/拷贝原目录内容到新建目录
- 赋权：chown -R mysql:mysql $PATH
- show global variables like '%datadir%';

#### lower_case_table_names

大小写敏感设置的属性，此参数不可以动态修改。
- unix,linux 默认值为 0
- Windows    默认值是 1
- MacOS      默认值是 2

查看官网 8.0 的文档（5.7.x 无此内容）：
- 在 Language Structure - Schema Object Names - Identifier Case Sensitivity 有记录：
- lower_case_table_names can only be configured when initializing the server. Changing the lower_case_table_names setting after the server is initialized is prohibited.
- 只能在初始化时指定 lower_case_table_names 参数，初始化之后该参数不允许修改。5.7 版本支持在初始化之后修改 lower_case_table_names 参数，而且还给出了在不同值下创建的数据库的迁移方案。而到了 8.0，只支持初始化时指定该参数，初始化之后，如果修改了该参数，启动就会报错，因为不允许在初始化之后修改这个值了。

　　如果不需要数据迁移：删除 data 目录下的所有文件，重新初始化。\\　　指定 lower_case_table_names 大小写不敏感的两种方式：
- 初始化设置 lower_case_table_names=1
  - /usr/local/mysql/bin/mysqld  --defaults-file=/etc/my.cnf --initialize-insecure --user=mysql --initialize --lower-case-table-names=1
  - my.cnf，在 [mysqld] 配置节点下添加 lower-case-table-names=1
