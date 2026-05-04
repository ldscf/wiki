---
source_title: Python 包
categories:
- Develop
- Python
last_modified: '2025-11-07T00:53:39Z'
---
有超过 200,000 个 Python 程序包（基于官方 Python 程序包索引 PyPI 托管的程序包）。

### Package
```
 python3 -m pip install [--upgrade] Package-Name
 pip3 install [--upgrade] Package-Name
```
 
```
 # 查看所有包安装路径
 python3 -m site
```
 
```
 # 查看包加载路径
 PACKAGE=piexif
 python3 -c "import ${PACKAGE}; print(${PACKAGE}.__file__)" 
```
 
```
 # 系统级包路径
 /usr/lib/python3.10/dist-packages
 /usr/local/lib/python3.10/dist-packages
 # 一般来说，用 root/sudo 安装的包，在系统级路径下，共享给所有用户使用。
```

#### 科学计算

| Package | Install | Import | Memo |
| Table | pandas | 处理复杂的数据集 |
| Matrix | numpy | 维度数组与矩阵运算 |
| Scientific | scipy | 插值、积分、优化、图像处理、统计、常微分方程 |
| Sklearn | scikit-learn | sklearn | 分类、回归、聚类、降维等算法机器学习库 |

#### DB

| Package | Install | Import | Memo |
| Oracle | oracledb | 支持 DSN 参数 |
| Oracle | cx_Oracle | 历史版本 |
| postgreSQL | psycopg2-binary | psycopg2 |
| MySQL | pymysql |
| Clickhouse | clickhouse_driver |
| SQL Server | pymssql |
| SQLite3 | sqlite3 |
| redis | redis-py-cluster | rediscluster | 集群 |
| redis | redis | 单机及哨兵 |

#### HTTP

| Package | Install | Import | Memo |
| HTTP | urllib3 | HTTP 客户端，SSL/TLS 验证等 |
| HTTP | requests | 发送HTTP请求 |

#### Other

| Package | Install | Import | Memo |
| YAML | pyyaml | yaml | 配置文件、数据存储和数据传输 |
| BeautifulSoup | BeautifulSoup4 | bs4 | 解析和导航 HTML/XML 结构，将 HTML 文档转换成一个 [[DOM]] 树形结构 |

#### 支持的安装包版本
```
 pip debug --verbose
```

### Build

using setup.py if you have downloaded the source package locally:
```
 python setup.py build
 python setup.py install
```

### Error

#### nltk.download
```
 [nltk_data] Error loading stopwords: 
```
```
 # 忽略证书验证
 # 建立 SSL 连接时，使用未经验证的上下文，从而避免 SSL 证书验证失败的错误
 import ssl
 ssl._create_default_https_context = ssl._create_unverified_context
```
 
```
 -.OR.-
```
 
```
 # 下载 Python 根证书
 curl --remote-name --time-cond cacert.pem https://curl.se/ca/cacert.pem
 替换这个目录的证书：
 import ssl
 ssl.get_default_verify_paths().openssl_cafile
```

#### ModuleNotFoundError

ModuleNotFoundError: No module named 'piexif'。事实上，包是安装过的。只不过在某些用户下可以执行，但在 root 下反而报错。可以查看包的加载路径确认，删除后再在 root 下安装成系统级包路径后正常。
```
 # python3 -m pip install pillow
 Requirement already satisfied: pillow in /usr/local/lib/python3.10/dist-packages (10.3.0)
 # pip3 uninstall pillow
 # python3 -m pip install pillow==10.3.0
```
