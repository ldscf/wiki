---
source_title: Oracle In Python
categories:
- Develop
- Oracle
- Python
last_modified: '2025-07-29T02:57:34Z'
---
Python 连接到 Oracle 数据库的驱动程序 oracledb 和 cx_Oracle 均提供了 DB API 2.0 兼容的接口。
1. cx_Oracle 是一个早推出的驱动程序，被广泛使用，用 C 语言编写。只支持 Thick 模式，意味着必须单独下载、安装和配置 Oracle 客户端库(Oracle Instant Client、Oracle 客户端或 Oracle 数据库安装其一)。cx_Oracle 仍然会得到维护（错误修复和安全更新）
1. oracledb 由 Oracle 公司在 2022 年发布，Python 原生实现，是 cx_Oracle 的后续版本，新功能会在 oracledb 中添加。最重要的改进是同时支持 Thin 和 Thick 两种驱动模式，Thin 模式是以纯 Python 实现的，通过 Python 的网络套接字与 Oracle 数据库通信，大大简化了安装和部署，尤其是在容器化环境或云环境中。在Thick 模式下支持所有特性，性能等同 cx_Oracle。

### cx_Oracle
```
 # 需要安装 Oracle Instant Client
 import cx_Oracle as oc
 conn = oc.connect(s_user + '/' + s_passwd + '@' + s_host + ':' + s_port + '/' + s_db)
```

### oracledb

python-oracledb 驱动程序的默认精简模式(Thin mode)，不仅不用安装客户端库，还可以在没有 Wallet 的情况下将 Python 应用程序连接到 Autonomous Database 实例。
1. 确定 Autonomous Database 实例已启用 TLS 连接  

在 Oracle Cloud Infrastructure 控制台的 “网络 ”区域中，“双向 TLS （mTLS） 验证 ”字段显示： 不需要  

Mutual TLS (mTLS) authentication field shows: Not Required
1. 获取 Autonomous Database 服务连接字符串以访问数据库(dsn)
```
 import oracledb as oc
```
 
```
 # 指定 Instant Client 的路径来启用 Thick 模式
 # 可选: oc.init_oracle_client(lib_dir="/opt/oracle/instantclient_21_12")
 # 配置钱包目录
 # 可选: os.environ["TNS_ADMIN"] = "/home/user/wallets/adb_wallet"
```
 
```
 conn = oc.connect(user=s_user, password=s_passwd, dsn=s_dsn)
```

### Sample
 ```
import oracledb
user = "admin"
password = "password"
dsn = """
(description=
    (retry_count=20)(retry_delay=3)
    (address=(protocol=tcps)(port=1522)(host=adb.uk-london-1.oraclecloud.com))
    (connect_data=(service_name=8192f_bidb_high.adb.oraclecloud.com))
    (security=(ssl_server_dn_match=yes))
)
"""
try:
    # mode=oracledb.THIN
    with oracledb.connect(user, password, dsn, mode=oracledb.THIN) as connection:
        with connection.cursor() as cursor:
            cursor.execute("SELECT sysdate FROM dual")
            for row in cursor:
                print(row)
except oracledb.DatabaseError as e:
    print(f"Database error: {e}")
```
