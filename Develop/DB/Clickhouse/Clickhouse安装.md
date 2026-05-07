---
source_title: Clickhouse安装
categories:
- Clickhouse
- DB
- Develop
last_modified: '2024-07-22T07:08:11Z'
---
ClickHouse 是一个用于联机分析(OLAP)的列式数据库管理系统(DBMS)。由俄罗斯 Yandex 于 2016 年开源，使用 C++ 语言编写，主要用于在线分析处理查询（OLAP）。

### 安装

#### 系统要求

ClickHouse 可以在任何具有 x86_64，AArch64 或 PowerPC64LE CPU 架构的 Linux，FreeBSD 或 Mac OS X 上运行。官方预构建的二进制文件通常针对 x86_64 进行编译，并利用SSE 4.2指令集：
```
 grep -q sse4_2 /proc/cpuinfo && echo "SSE 4.2 supported" || echo "SSE 4.2 not supported"
```

#### Inst
```
 curl https://clickhouse.com/ | sh
```

#### Password

Password for default user is saved in file /etc/clickhouse-server/users.d/default-password.xml
```
 
```
     
         
             
             ????</password_sha256_hex>
         
     
```
 
```
 
```
 # ???? = echo -n "$PASSWORD" | sha256sum
 # 或者也可以将  --> ，这样可以使用明文密码。
```
 
```
 # 如果要增加密码，类似于  ...  即可。
```

### 启动
```
 clickhouse-server start
 clickhouse-server stop
```

### 卸载
```
 FL=(
 /usr/bin/clickhouse*
 /etc/clickhouse-server
 /var/lib/clickhouse/
 /var/log/clickhouse-server/
 /etc/security/limits.d/clickhouse.conf
 /root/.clickhouse-client-history
```

)

for F in ${FL[*]};do

  rm -rf $F

done
