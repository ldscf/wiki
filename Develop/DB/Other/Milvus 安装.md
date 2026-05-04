---
source_title: Milvus 安装
categories:
- DB
- Develop
- OtherDB
last_modified: '2024-08-15T08:56:43Z'
---
Milvus is an open-source vector database that brings search to GenAI applications.

Milvus was selected as the vector database of choice (over Chroma and Pinecone). Milvus is an open-source vector database designed specifically for similarity search on massive datasets of high-dimensional vectors.

Milvus supports Python, Java, C++.

### Milvus 2

[HELP: Run Milvus in Docker](https://milvus.io/docs/install_standalone-docker.md)
- Preparations
1. Milvus 2.0.0
1. Python 3 (3.7.1 or later)
1. PyMilvus 2.0.0

#### Docker Inst
- 拉取并保存镜像
```
 docker pull milvusdb/milvus:v2.4.5
```
- 下载脚本文件
```
 wget https://raw.githubusercontent.com/milvus-io/milvus/master/scripts/standalone_embed.sh
 bash standalone_embed.sh start | stop | delete
```
- 路径

在 standalone_embed.sh 目录下创建 volumes 目录，存放数据文件。
 ```

# docker ps 
0cc9050d3dc4   milvusdb/milvus:v2.4.5               "/tini -- milvus run…"   4 minutes ago   Up 4 minutes (healthy)   0.0.0.0:2379->2379/tcp, :::2379->2379/tcp, 0.0.0.0:9091->9091/tcp, :::9091->9091/tcp, 0.0.0.0:19530->19530/tcp, :::19530->19530/tcp   milvus-standalone

# docker logs 0cc9050d3dc4
[2024/08/15 07:48:30.255 +00:00] [INFO] [distance/calc_distance_amd64.go:14] ["Hook avx for go simd distance computation"]
2024/08/15 07:48:30 maxprocs: Leaving GOMAXPROCS=6: CPU quota undefined
    __  _________ _   ____  ______    
   /  |/  /  _/ /| | / / / / / __/    
  / /|_/ // // /_| |/ / /_/ /\ \    
 /_/  /_/___/____/___/\____/___/     
Welcome to use Milvus!
Version:   v2.4.5
Built:     Wed Jun 19 02:47:56 UTC 2024
GitCommit: 60695bdb
GoVersion: go version go1.20.7 linux/amd64
TotalMem: 12537851904
UsedMem: 26251264
open pid file: /run/milvus/standalone.pid
lock pid file: /run/milvus/standalone.pid
```

#### V2 Sample

[Run Milvus using Python](https://milvus.io/docs/v2.0.x/example_code.md)

### Milvus 1

#### Docker Inst
```
 docker pull milvusdb/milvus:cpu-latest
```
```
 # 设置配置文件和工作目录
 /u01/milvus/conf
   : conf      # [server_config.yaml](https://github.com/milvus-io/milvus/blob/v0.10.1/core/conf/demo/server_config.yaml)
   : db        # 索引与向量存储
   : logs      # 日志
   : wal       # 预写式日志
```
```
 docker run -td --name mymilvus -e "TZ=Asia/Shanghai" -p 19530:19530 -p 19121:19121 \
  -v /u01/milvus/db:/var/lib/milvus/db \
  -v /u01/milvus/wal:/var/lib/milvus/wal \
  -v /u01/milvus/logs:/var/lib/milvus/logs \
  -v /u01/milvus/conf:/var/lib/milvus/conf \
  milvusdb/milvus:cpu-latest
```
```
 *<small># docker ps
 CONTAINER ID   IMAGE                        COMMAND                  CREATED         STATUS          PORTS                                                                                          NAMES
 f8e350986ef4   milvusdb/milvus:cpu-latest   "/tini -- /var/lib/m…"   4 seconds ago   Up 3 seconds   0.0.0.0:19121->19121/tcp, :::19121->19121/tcp,  0.0.0.0:19530->19530/tcp, :::19530->19530/tcp   milvusdev
 322f00c39f82   registry                     "/entrypoint.sh /etc…"   2 weeks ago     Up 13 days     0.0.0.0:8000->5000/tcp, :::8000->5000/ tcp                                                      uhry
```

 
```
 # docker logs f8e350986ef4
     __  _________ _   ____  ______    
    /  |/  /  _/ /| | / / / / / __/    
   / /|_/ // // /_| |/ / /_/ /\ \    
  /_/  /_/___/____/___/\____/___/     
```

 
```
 Welcome to use Milvus!
 Milvus Release version: v1.1.1, built at 2021-06-15 14:51.05, with OpenBLAS library.
 You are using Milvus CPU edition
 Last commit id: 330cc61bede475c4a7a71841d54e633586cea829
```

 
```
 Loading configuration from: /var/lib/milvus/conf/server_config.yaml
 NOTICE: You are using SQLite as the meta data management. We recommend change it to MySQL.
 Supported CPU instruction sets: avx2, sse4_2
 FAISS hook AVX2
 Milvus server started successfully!*
```

#### V1 Sample

milvus 版本 1.x 与 2.0 不兼容，pymilvus 也如此，且低版本的 pymilvus 不可安装在 python 高版本上，如 11。下面使用 pymilvus 1.1.0，安装在 python3.6 上。
 ```

# -*- coding: utf-8 -*-
import numpy as np
from milvus import Milvus, MetricType
HOST='192.168.0.242'
PORT=19530
milvus = Milvus(host=HOST, port=PORT)

# create table
num_vec = 5000
vec_dim = 768
collection_name = "demo1"
collection_param = {
'collection_name': collection_name,
'dimension': vec_dim,
'index_file_size': 32,
'metric_type': MetricType.IP
}
milvus.create_collection(collection_param)

# Generate random data
vectors_array = np.random.rand(num_vec, vec_dim)

# Insert DB
status, ids = milvus.insert(collection_name=collection_name, records=vectors_array)   # 返回状态和这一组向量的ID
milvus.flush([collection_name])
print(milvus.get_collection_stats(collection_name))

# QUERY
query_vec_array = np.random.rand(1, vec_dim)
status, results = milvus.search(collection_name=collection_name, query_records=query_vec_array, top_k=5)
print(status)
print(results)

# Drop Table

# status = milvus.drop_collection(collection_name)
milvus.close()
```
