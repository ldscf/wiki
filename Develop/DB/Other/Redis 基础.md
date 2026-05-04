---
source_title: Redis 基础
categories:
- DB
- Develop
- OtherDB
- Redis
last_modified: '2025-06-18T05:13:20Z'
---
### 基本类型：

#### String(字符串)

set key value/get key
- Max: 512M
- 内部编码有3种，int（8字节长整型）、embstr（<=39 Byte）、raw（>39 Byte）
- 应用场景: 共享session、分布式锁、计数器、限流
- C 语言的字符串是 char[] 实现的，而 Redis 使用 SDS(Simple Dynamic String)结构：
```
 struct sdshdr {
    unsigned int len;  // 字符串长度
    unsigned int free; // 空闲长度
    char buf[];        // 字符串
 }
```

#### Hash(哈希)

哈希类型是指 value 本身又是一个键值对(k-v)结构

hset key field value/hget key field/hscan key
- 底层编码：ziplist（压缩列表） 、hashtable（哈希表）
- 应用场景：缓存用户信息等
- 注意：哈希元素比较多的话，使用 hgetall 可能会导致 Redis 阻塞，可以使用 hscan。若只是获取部分 field，使用 hmget

#### List(列表)

有序多个元素，最多可以存储 2^32-1 个元素

lpush key value[value ...] 、lrange key start end
- 底层编码：ziplist（压缩列表）、linkedlist（链表）
- 应用场景：消息队列，文章列表
1. Stack(栈): lpush + lpop
1. Queue(队列): lpush + rpop
1. Capped Collection(有限集合): lpush + ltrim
1. Message Queue(消息队列): lpush + brpop

#### Set(集合)

元素不重复

sadd key element[element...]、smembers key
- 底层编码：intset(整数集合)、hashtable(哈希表)
- 应用场景：用户标签、生成随机数抽奖、社交需求
- 注意：元素比较多的话，使用 smembers 和 lrange、hgetall 可能会导致 Redis 阻塞，可以使用 sscan

#### zset(有序集合)

zadd key score member[score member...]，zrank key member
- 底层编码：ziplist(压缩列表)、skiplist(跳跃表)
- 应用场景：排行榜，社交需求(如用户点赞)

### 数据结构类型
- Geospatial
- Hyperloglog
- Bitmap

### Redis 持久化方式

将内存中的数据保存到硬盘或其他长期存储介质中，在 redis.conf 中配置。

#### RDB

RDB (Redis Database Snapshot) 将当前内存中的数据快照（snapshot）保存到硬盘
- 手动触发：通过执行 save 或 bgsave 命令
- 自动触发：基于 Redis.conf 中的 save 指令设置的条件

当 Redis 重新启动时，如果配置为使用 RDB 持久化，它会查找 RDB 文件，并加载。由于 RDB 文件是一个紧凑的二进制表示形式，数据加载非常快。
```
 save            # save 600 1: 600 秒内如果超过 1 个 key 被修改则生成 RDB
 stop-writes-on-bgsave-error no    # 如果后台保存数据出现错误，Redis 将不停止写入操作
 rdbcompression yes                # 在保存数据前先对其进行压缩
 rdbchecksum yes                   # 在 RDB 文件末尾添加一个 CRC64 校验和
 sanitize-dump-payload yes         # 在加载数据时检查这些数据的安全性
 dbfilename dump.rdb               # RDB 文件名 (默认是 dump.rdb)
 rdb-del-sync-files yes            # 主节点没有启用 RDB 和 AOF 持久化，Redis 自动删除复制相关 RDB 文件
 dir /var/redis/data/              # RDB 文件存放的目录，默认值 ./ (/var/lib/redis 启动时的工作目录)
```

#### AOF

AOF (Append Only File) 持久化方式旨在持续地保存服务器上的所有修改操作。每当执行一个会改变数据的命令时，Redis 都会将该命令写入 AOF 文件中。这样，当 Redis 需要恢复数据时，只需执行 AOF 文件中的命令就可以恢复到原来的状态。AOF是一个文本文件，里面保存了一系列的命令。
```
 appendonly yes                       # default no
 appendfilename appendonly.aof        # default
 appendfsync everysec                 # default
 no-appendfsync-on-rewrite yes        # 避免主进程 fsync 延迟
```

同步策略:
- always：每次有命令写入时都立即同步
- everysec：每秒同步一次，权衡安全性和效率的策略
- no：让操作系统决定最佳的同步时间

#### 混合持久化

Redis 4.0 引入的持久化策略，结合了 RDB 的快速恢复和 AOF 的数据完整性的优点，它首先以 RDB 格式保存当前数据状态，然后继续以 AOF 格式记录新的写操作，确保数据完整性并优化恢复速度。
```
 # 同时启用 RDB 和 AOF 两种持久化
 aof-use-rdb-preamble yes
```

### 缓存三大典型问题

一般来说，缓存穿透是遇到坏人了，其他两个是遇到了不太合格的开发人员。

#### 缓存穿透

缓存穿透（Cache Penetration）指客户端请求的数据在缓存和数据库中都不存在，每次请求都穿透缓存直接请求数据库，对数据库造成巨大压力，甚至拖垮数据库。如：攻击者构造大量不存在的 ID 发起请求。

**解决方案：**
- 布隆过滤器 (Bloom Filter)：
  - 原理： 在查询数据库之前，先通过布隆过滤器判断请求的 Key 是否存在于数据库中。如果布隆过滤器判断 Key 不存在，则直接返回，避免查询数据库。
  - 优点： 空间效率高，查询速度快。
  - 缺点： 存在误判（即布隆过滤器说 Key 存在，但数据库实际不存在），无法删除 Key。
  - 适用： 大量查询不存在数据的场景。
- 缓存空对象 (Cache Empty Objects / Null Objects)：
  - 原理： 当数据库查询返回的数据为空（即该 Key 不存在）时，将这个空结果写入缓存，并设置一个较短的过期时间。
  - 优点： 实现简单，有效防止对同一不存在 Key 的重复查询。
  - 缺点： 可能会缓存大量空对象，占用缓存空间；需要额外的逻辑来处理空值。
  - 适用： 对缓存空间不敏感，或少量查询不存在数据的场景。
- 接口层校验：
  - 原理： 在数据查询的接口层对请求参数进行合法性校验，例如对 ID 进行范围检查，不合法的请求直接拒绝。
  - 优点： 简单有效，可以在请求到达缓存层之前就拦截掉非法请求。
  - 缺点： 只能拦截明显不合法的请求，无法拦截合法的但不存在的 Key。
  - 适用： 所有查询接口。

#### 缓存雪崩

缓存雪崩（Cache Avalanche）指大量 Key 同一时间失效，引发瞬时大量请求访问数据库。如：系统预热时，将所有商品的缓存都设置了相同的过期时间 TTL 导致同时过期。

**解决方案：**
- 错开缓存过期时间 (Randomize Cache Expiration)：
  - 原理： 给缓存的 Key 设置不同的过期时间，例如在基础过期时间上增加一个随机值（expire_time = base_time + random(offset)）。这样即使在高并发下，缓存失效也是错开的，不会在同一瞬间大量失效。
  - 优点： 实现简单，非常有效。
  - 缺点： 不能完全避免雪崩（如果随机范围不够大）。
  - 适用： 绝大多数存在大量缓存 Key 的场景。
- 降级和限流 (Degradation and Throttling)：
  - 原理： 当数据库负载达到一定阈值时，启用熔断降级策略，例如：
  - * 限流： 限制进入数据库的请求数量，多余的请求直接返回错误或默认值。
  - * 熔断： 暂停对数据库的访问，直接返回错误或缓存中的旧数据（即使已过期）。
  - * 降级： 牺牲部分功能，确保核心功能可用，例如暂停非关键数据的缓存更新，或返回静态页面。
  - 优点： 在极端情况下保护数据库不被压垮，避免全系统崩溃。
  - 缺点： 会牺牲用户体验或部分功能。
  - 适用： 作为最后一道防线，用于应对突发的高负载情况。
- 多级缓存 (Multi-level Caching)：
  - 原理： 引入多级缓存，例如本地缓存（Guava Cache/Caffeine）+ 分布式缓存（Redis）。当分布式缓存失效或宕机时，请求可以尝试命中本地缓存，或者返回本地缓存的旧数据。
  - 优点： 提高缓存命中率，进一步降低数据库压力，增加系统容错性。
  - 缺点： 引入缓存一致性问题，需要更复杂的失效和更新策略。
  - 适用： 对性能和可用性要求极高，且数据一致性要求不那么苛刻的场景。

#### 缓存击穿

缓存击穿（Cache Breakdown）指某个“热点Key”在某一时刻失效，由于缓存失效，大量“热点Key”请求落到数据库。如：热点商品详情页缓存失效，造成短时间大量请求打到数据库。

**解决方案：**
- 互斥锁 / 分布式锁 (Mutex Locks / Distributed Locks)：
  - 原理： 当缓存失效时，只有一个线程（或进程）能获得锁并去查询数据库，其他未能获得锁的线程则等待或者从缓存中返回旧值（如果有）。数据库查询完成后，更新缓存并释放锁。
  - 优点： 有效控制并发，避免大量请求同时访问数据库。
  - 缺点： 会阻塞其他请求，增加响应时间；如果锁机制本身出现问题（如死锁），可能导致服务不可用；实现复杂度相对较高。
  - 适用： 绝大多数热点 Key 场景。
- 热点数据永不失效 (Never Expire Hot Data)：
  - 原理： 对热点 Key 设置永不过期（或超长时间过期），当数据有更新时，通过消息队列或者数据库触发器等方式主动更新缓存。
  - 优点： 简单粗暴，彻底避免击穿。
  - 缺点： 无法处理热点 Key 发生变化的情况（例如，某个商品不再热门），需要额外的机制来保证数据一致性（即数据库更新后缓存也更新）。
  - 适用： 真正长期不变的热点 Key，或者需要极高可用性的核心数据。
- 异步更新 / 逻辑过期 (Asynchronous Update / Logical Expiration)：
  - 原理： 缓存中的 Key 依然有过期时间，但其数据结构中额外存储一个逻辑过期时间。当查询命中缓存但发现逻辑过期时，不立即删除缓存，而是启动一个后台线程去查询数据库并更新缓存。同时，当前请求仍然返回旧的缓存数据。
  - 优点： 不会阻塞用户请求，用户体验好；异步更新减轻数据库瞬时压力。
  - 缺点： 数据可能在短时间内不一致（用户拿到旧数据）；增加了代码复杂度。
  - 适用： 对数据一致性有一定容忍度，且需要保证高可用性的场景。
