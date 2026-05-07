---
source_title: ADW - Autonomous Database details
categories:
- DB
- Develop
- Oracle
last_modified: '2025-09-07T03:47:51Z'
---
Oracle Cloud ADW - Autonomous Database details

### Run Python Application Without a Wallet

#### Network

##### Access type:
- Allow secure access from specified IPs and VCNs

##### Access control list:
- Enabled IP: 36.112.25.131,132.145.51.204
- OR -
- CIDR: 0.0.0.0/0

##### Mutual TLS (mTLS) authentication:
- Not Required
```
 #OLD: DSN='(description= (retry_count=20)(retry_delay=3)(address=(protocol=tcps)(port=1522)(host=adb.uk-london-1.oraclecloud .com))(connect_data=(service_name=g0afda210a8192f_bidb_medium.adb.oraclecloud.com))(security=(ssl_server_cert_dn="CN=a dwc.eucom-central-1.oraclecloud.com, OU=Oracle BMCS FRANKFURT, O=Oracle Corporation, L=Redwood City, ST=California,  C=US")))'
 DSN='DSN='(description= (retry_count=20)(retry_delay=3)(address=(protocol=tcps)(port=1522)(host=adb.uk-london-1.oraclecloud.com))(connect_data=(service_name=g0afda210a8192f_bidb_medium.adb.oraclecloud.com))(security=(ssl_server_dn_match=yes)))
 USER='admin'
 PASSWD='orB.db.1.2..8'
```
 
```
 import oracledb
```
 
```
 conn=oracledb.connect(user=USER, password=PASSWD, dsn=DSN)
 cur=conn.cursor()
 res=cur.execute("select count(*) from tab")
 for TN in res:
```

     print(TN)

### Run Toad/PLSQL With a Wallet

#### SYS EVN
```
 NLS_LANG = SIMPLIFIED CHINESE_CHINA.ZHS16GBK
 path = %ORACLE_HOME%
 TNS_ADMIN = %ORACLE_HOME%\network\admin
```

#### Wallet

wallet_*.zip 文件解压放入 TNS_ADMIN

### Run DBeaver With a Wallet

See Also: [[DBeaver#Run DBeaver With a Datafuse jar|DBeaver]]

### Keep OracleDB

#### Shell
```
 cd /u01/git/pangolin/
 ./dbct mc_bidb 'select * from test'
```

#### Crontab
```
 0 1 * * * sh ~/bin/keep_oradb.sh >/dev/null 2>&1
```

### Error

#### AttributeError: module 'lib' has no attribute 'X509_V_FLAG_CB_ISSUER_CHECK'

系统当前的 python 和 pyOpenSSL 版本不对应
```
 mv /usr/lib/python3/dist-packages/OpenSSL /tmp/
```
 
```
 pip3 install pyOpenSSL --upgrade
```
```
 Collecting pyOpenSSL
```

   Downloading pyOpenSSL-22.1.0-py3-none-any.whl (57 kB)

      |████████████████████████████████| 57 kB 5.5 MB/s 
```
 Requirement already satisfied, skipping upgrade: cryptography<39,>=38.0.0 in /usr/local/lib/python3.8/dist-packages ( from pyOpenSSL) (38.0.4)
 Requirement already satisfied, skipping upgrade: cffi>=1.12 in /usr/local/lib/python3.8/dist-packages (from  cryptography<39,>=38.0.0->pyOpenSSL) (1.15.0)
 Requirement already satisfied, skipping upgrade: pycparser in /usr/local/lib/python3.8/dist-packages (from  cffi>=1.12->cryptography<39,>=38.0.0->pyOpenSSL) (2.21)
 Installing collected packages: pyOpenSSL
```

   Attempting uninstall: pyOpenSSL

     Found existing installation: pyOpenSSL 19.0.0

     Not uninstalling pyopenssl at /usr/lib/python3/dist-packages, outside environment /usr

     Can't uninstall 'pyOpenSSL'. No files were found to uninstall.
```
 Successfully installed pyOpenSSL-22.1.0
```

<!-- template: DEFAULTSORT:ADW_Autonomous_Database_details -->
