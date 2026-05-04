---
source_title: Python redis
categories:
- DB
- Develop
- OtherDB
- Python
- Redis
last_modified: '2025-03-19T01:01:29Z'
---
### Connect

#### 集群
 ```

# pip3 install rediscluster       # 老版本及新版本

# pip3 install redis-py-cluster   # 中间版本
 
import rediscluster
rnode = [
    {"host": "192.168.0.10", "port": 6379},
    {"host": "192.168.0.11", "port": 6380},
    {"host": "192.168.0.11", "port": 6379},
    {"host": "192.168.0.12", "port": 6380},
    {"host": "192.168.0.12", "port": 6379},
    {"host": "192.168.0.10", "port": 6380}
]
try:
    rc = rediscluster.RedisCluster(startup_nodes=rnode, decode_responses=True, password="abc123")
    # 测试连接，所有节点都响应 PONG
    print(rc.ping())
    # rc.set("mykey", "myvalue")
    # print(rc.get("mykey"))
except rediscluster.exceptions.RedisClusterException as e:
    print(f"Error connecting to Redis cluster: {e}")
except Exception as e:
     print(f"An unexpected error occurred: {e}")

# 默认情况下，rediscluster 库返回的是字节串 (bytes)。 设置 decode_responses=True 会自动将响应结果解码为 Python 字符串，这在大多数情况下更方便。
```

#### 单机

Redis 的单机使用在集群上时，如果读的数据不在连接主机上，会出现错误。
```
 # pip3 install redis
 import redis
 rc = redis.Redis(host='192.168.0.229', port=6379, db=0)
```

#### 密码
```
 rc = redis.Redis(host='192.168.0.229', port=6379, db=0, password='passwd')
 # rc.execute_command('auth', 'passwd')
 # rc.auth('passwd')
```

### 操作

#### Connection Pool

使用连接池来管理所有连接，避免每次建立、释放连接的开销。
```
 # redis
 rcp = redis.ConnectionPool(host='192.168.0.229', port=6379, db=0)
 rc = redis.Redis(connection_pool = rcp, decode_responses=True)
 rc2 = redis.Redis(connection_pool = rcp, decode_responses=True)
 # redis cluster
 rcp = rediscluster.ClusterConnectionPool(startup_nodes=rnode, decode_responses=True)
 rc = rediscluster.RedisCluster(connection_pool=rcp)
```

#### 管道

Redis 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应。批量写入时,使用pipeline可以大幅度提升性能
```
 # 使用 rp 代替 rc
 rp = rc.pipeline(transaction = False)
 # 足够数量的 rp.set 后，提交
 FLAG = rp.execute()
```

#### set
```
 rc.set('test', 'Hello, World!')
 rc.set('test1', 'Hello, Redis!')
 rc.set('test2', 'Hello, RDS!')
```

#### get
```
 rc.get('test')
```

#### keys

返回集合键某一时刻包含的所有元素。当 KEYS 命令被用于处理一个大的数据库时， 又或者 SMEMBERS 命令被用于处理一个大的集合键时， 它们可能会阻塞服务器达数秒之久。
```
 rc.keys('*test*')
 rc.keys('*test?')
```

#### scan

增量式遍历命令。增量式遍历的过程中，键可能会被修改，不能完全保证返回所有元素。被调用之后，会向用户返回一个新的游标（ 0 视作结束），用户在下次遍历时需要使用这个新游标作为 SCAN 命令的游标参数，以此来延续之前的遍历过程。
```
 rc.scan(0, '*0123*', 10)
 {'192.168.0.229:6379': (6292935, ['86.56789.410/154710123', '86.56789.410/117012344']), '192.168.0.148:6379': (2099409, ['86.56789.410/121501232', '86.56789.410/110123399']), '192.168.0.249:6379': (6293256, ['86.56789.410/129012348', '86.56789.410/199530123'])}
```

#### delete
```
 rc.delete('test2')
```

#### 清空
```
 rc.flushdb()
 rc.flushall()
```

#### 各节点记录数
```
 rc.dbsize()
```

#### 有效时间

100 secs
```
 # rc.set('test', 'Hello, World!', 100)
 rc.set('test', 'Hello, World!')
 rc.expire('test', 100)
 rc.get('test')
```

### Error
- WRONGTYPE Operation against a key holding the wrong kind of value

错误类型操作，如用 get 一个 hash key 即会出现此类错误
```
 rc.hset('htest', 'a', 1)
 rc.hset('htest', 'b', 2)
```
```
 rc.get('htest')
 --> 
 rc.hget('htest', 'a')
```
- UnicodeDecodeError: 'utf-8' codec can't decode byte 0xac in position 0: invalid start byte
```
 rc = RedisCluster(startup_nodes=rnode, decode_responses=True, encoding='ISO-8859-1')
```
