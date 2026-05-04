---
source_title: Redis
categories:
- DB
- Develop
- OtherDB
- Redis
last_modified: '2026-03-16T01:10:54Z'
---
[Redis](https://redis.io/)(Remote Dictionary Server, 远程字典服务)，是一个开源的使用 ANSI C 语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value 数据库。

Redis 的数据是存在内存中的，读写速度每秒可处理超过 10 万次读写操作。广泛应用在应用缓存领域，也经常用来做分布式锁。除此之外，Redis 支持事务、持久化、LUA 脚本、LRU 驱动事件、多种集群方案。> The open source, in-memory data store used by millions of developers as a database, cache, streaming engine, and message broker.

### License

2024 年 3 月 20 日，[Redis](https://github.com/redis/redis) 项目的开源协议发生了重大改变，从非常宽松的 BSD 转为 Redis 源代码可用许可证(RSALv2)和服务器端公共许可证(SSPLv1)下双重许可。在新的许可证下，托管云服务提供商不再被允许免费使用 Redis 的源代码。

### Redis Stack

Redis Stack 扩展了 Redis OSS 的核心功能，更加专注于构建实时应用程序，并为调试等提供了完整的开发人员体验。
- Redis Stack Server，包括：Redis，RedisSearch，RedisJSON，RedisGraph，RedisTimeSeries 和 RedisBloom等
- RedisInsight
- Redis Stack Client SDK

### Install

#### Redis Stack
```
 curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
 chmod 644 /usr/share/keyrings/redis-archive-keyring.gpg
 echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list
 apt-get update
 apt-get install redis-stack-server
 systemctl start redis-stack-server.service 
 ## 外网访问
 # /etc/redis-stack.conf
 bind * -::* 
 protected-mode no
```

#### Redis Enterprise
- https://redis.io/docs/latest/operate/rs/installing-upgrading/install/install-on-linux/
- wget https://redis.io/docs/latest/operate/rs/installing-upgrading/install/GPG-KEY-redislabs-packages.gpg
- gpg --import GPG-KEY-redislabs-packages.gpg
```
 *<<gpg: directory '/root/.gnupg' created
 gpg: keybox '/root/.gnupg/pubring.kbx' created
 gpg: /root/.gnupg/trustdb.gpg: trustdb created
 gpg: key EC5EC593D7D1529F: public key "Redis Labs Package Signing Key (2020) <support@redislabs.com>" imported
 gpg: Total number processed: 1
 gpg:               imported: 1*
```
- dpkg-sig --verify redislabs_7.4.2-129~focal_amd64.deb
```
 *<<Processing redislabs_7.4.2-129~focal_amd64.deb...
 GOODSIG _gpgorigin 5E8EFA2409E5C44FB529BE20EC5EC593D7D1529F 1712603737*
```
- install.sh
- start
```
 # /opt/redislabs/bin/
 redis-server
```

#### Redis6.2

在 macOS 上安装特定版本，Homebrew 出于安全考虑不会自动将它的命令（如 redis-cli, redis-server）软链接到系统标准路径中。
 ```

# 更新 brew 仓库
brew update

# 安装 redis
brew install redis@6.2

# 后台运行
brew services start redis

# 将路径写入配置文件
echo 'export PATH="/opt/homebrew/opt/redis@6.2/bin:$PATH"' >> ~/.zshrc
```

### Redis Shell

#### 可执行文件

| 可执行文件 | 作用 |
| redis-server | 启动redis |
| redis-cli | redis命令行工具 |
| redis-benchmark | 基准测试工具 |
| redis-check-aof | AOF持久化文件检测工具和修复工具 |
| redis-check-dump | RDB持久化文件检测工具和修复工具 |
| redis-sentinel | 启动redis-sentinel |

#### redis-cli
```
 redis-cli [-h {host} -p {port} {command}]
 redis-cli -help
```

##### Passwd
```
 redis-cli 
 # 认证密码
 auth "密码"
 # 查看密码
 config get requirepass
 # 修改密码
 config set requirepass "密码"
```

##### 参数

| 命令 | 说明 | 同义词 |
|:---|:---|:---|
| + |
| -r | 重复执行命令的次数 | --repeat |
| -i | 命令执行的间隔时间 | --interval |
| -x | 从标准输入(stdin)读取最后一个参数 |
| --raw | 以原始格式打印返回结果 |
| --csv | 以 CSV 格式打印返回结果 |

##### Sample
- 版本: redis-cli info | grep redis_version
- 特别参数: redis-cli keys '*'
- 执行命令: redis-cli lpush l1 1 2 3
- 定时重复执行命令: redis-cli -r 5 -i 1 incr cr1
- 原始格式: redis-cli --raw lrange l1 0 5
- Excel 格式: redis-cli --csv lrange l1 0 5
