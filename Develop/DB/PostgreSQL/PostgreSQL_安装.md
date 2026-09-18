---
source_title: PostgreSQL 安装
categories:
- DB
- Develop
- PostgreSQL
last_modified: '2026-09-17T02:16:16Z'
---
## 环境信息
* **操作系统**：Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-138-generic x86_64)
* **数据库**：PostgreSQL 18

## 安装前准备

### 添加 PostgreSQL 官方 APT 源

执行以下命令添加 PGDG 官方仓库：

install -d /usr/share/postgresql-common/pgdg

curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc

echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt jammy-pgdg main" > /etc/apt/sources.list.d/pgdg.list

### 更新软件包列表

apt update

更新过程中会拉取官方源：
- https://apt.postgresql.org/pub/repos/apt jammy-pgdg

## 安装 PostgreSQL 18

使用非交互方式安装：

DEBIAN_FRONTEND=noninteractive apt install -y postgresql-18

### 安装的主要软件包

| 软件包 | 版本 | 说明 |
|:---|:---|:---|
| postgresql-18 | 18.6-1.pgdg22.04+2 | PostgreSQL 服务器主程序 |
| postgresql-18-jit | 18.6-1.pgdg22.04+2 | JIT 编译支持 |
| postgresql-client-18 | 18.6-1.pgdg22.04+2 | 客户端工具 |
| postgresql-client-common | 293.pgdg22.04+1 | 客户端公共组件 |
| postgresql-common | 293.pgdg22.04+1 | 通用管理工具 |
| libpq5 | 18.6-1.pgdg22.04+2 | 客户端库 |
| sysstat | 12.5.2-2ubuntu0.2 | 系统性能统计工具（依赖） |

### 安装过程关键操作
- 创建用户 postgres 并加入 ssl-cert 组
- 自动创建配置文件 /etc/postgresql-common/createcluster.conf
- 创建 systemd 服务链接：
  - /etc/systemd/system/multi-user.target.wants/postgresql.service
- 初始化数据库集群 18/main

### 初始化详情

安装过程中自动执行：

/usr/lib/postgresql/18/bin/initdb -D /var/lib/postgresql/18/main --auth-local peer --auth-host scram-sha-256 --no-instructions

### 初始化参数

LC_MONETARY / LC_NUMERIC / LC_TIME = zh_CN.UTF-8

| 参数 | 值 |
|:---|:---|
| 数据目录 | /var/lib/postgresql/18/main |
| 本地认证方式 | peer |
| 主机认证方式 | scram-sha-256 |
| 区域设置 | LC_COLLATE / LC_CTYPE / LC_MESSAGES = en_US.UTF-8 |
| 默认编码 | UTF8 |
| 文本搜索配置 | english |
| 数据页校验和 | 已启用 |
| 共享内存实现 | posix |
| max_connections（默认） | 100 |
| shared_buffers（默认） | 128MB |
| 时区 | Asia/Shanghai |

### 服务状态确认

安装完成后检查服务状态：

systemctl status postgresql

预期输出示例：

● postgresql.service - PostgreSQL RDBMS

     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)

     Active: active (exited) since Thu 2026-09-17 09:25:05 CST; ...

服务已设置为开机自启，并处于 active 状态。

## 常用管理命令

| 操作 | 命令 |
|:---|:---|
| 启动服务 | systemctl start postgresql |
| 停止服务 | systemctl stop postgresql |
| 重启服务 | systemctl restart postgresql |
| 查看状态 | systemctl status postgresql |
| 切换到 postgres 用户 | su - postgres |
| 进入 psql | psql（postgres 用户下） |
| 查看集群列表 | pg_lsclusters |

## 创建数据库

进入 psql 命令终端
```
 su - postgres -c "psql"
```
 ```
-- 1. 创建用户并设置密码
CREATE USER appuser WITH PASSWORD 'abcd1234';
-- 2. 创建数据库并指定所有者
CREATE DATABASE appdb OWNER appuser;
-- 3. 授予数据库所有权限
GRANT ALL PRIVILEGES ON DATABASE appdb TO appuser;
-- 4. 切换到 appdb 数据库
\c appdb
-- 5. 授予 public schema 的所有权限（PostgreSQL 15+ 必须）
GRANT ALL ON SCHEMA public TO appuser;
-- 6. 退出 psql
\q
```

连接验证
```
 PGPASSWORD='abcd1234' psql -h 127.0.0.1 -U appuser -d appdb -c 'SELECT current_user, current_database();'
```

## 允许远程访问
```
 # /etc/postgresql/18/main/postgresql.conf
 listen_addresses = '*'
 # /etc/postgresql/18/main/pg_hba.conf
 host    all             all             192.168.0.0/24          scram-sha-256
```

systemctl restart postgresql

## 注意事项
- 官方源不支持 i386 架构，会出现相关跳过提示，属正常现象。
- 默认仅监听本地连接，远程访问需额外配置 postgresql.conf 与 pg_hba.conf。

## 参考链接
- [PostgreSQL 官方 Ubuntu 安装文档](https://www.postgresql.org/download/linux/ubuntu/)
- [PostgreSQL APT 仓库](https://apt.postgresql.org/)
- [Ubuntu 官方文档](https://help.ubuntu.com)
