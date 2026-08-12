---
source_title: Java 自定义方法 - KAFKA
categories:
- Develop
- Java
last_modified: '2024-10-30T08:05:25Z'
---
Java 自定义函数 - KAFKA

### Producer

producer 是线程安全的。
```
 Producer producer
```

#### 消息发送方式
```
 # 异步，默认方法
 producer.send(new ProducerRecord(topic1, s));
 # 异步，回调
 producer.send(new ProducerRecord(topic1, s), (RecordMetadata metadata, Exception e) -> {});
 # 同步
 RecordMetadata metadata = producer.send(new ProducerRecord(topic1, s)).get();
```

#### 消息分区策略

消息键保序(key-ordering )策略：Kafka 中每条消息都可以定义 Key，同一个 Key 的所有消息都进入到相同的分区中。否则采用轮询(Round-robin)、随机策略(Randomness)等策略。
```
 producer.send(new ProducerRecord(TOPIC, key, val)
 -.OR.-
 producer.send(new ProducerRecord(TOPIC, val)
```

#### 集群同步规则

ack 配置项用来控制 producer 要求 leader 确认多少消息后返回调用成功
- 0 不需要等待任何确认消息
- 1 需要等待leader确认
- -1或all 需要全部 ISR 集合返回确认，默认值
```
 ack=-1         # 默认需要全部 ISR 集合返回确认
```

#### 缓冲区

缓冲一批数据发送或超过延时时间(linger.ms=0)发送，默认 16k。
```
 batch.size=1048576    # 1M
 linger.ms=10          # 10ms
```

#### 消息重发

消息发送错误时设置的系统重发消息次数(retries=2147483647)、重发间隔(retry.backoff.ms=100)。若保证消息的有序性，设置 max_in_flight_requests_per_connection=1。
```
 max_in_flight_requests_per_connection=1
 retries=10
 retry.backoff.ms=200
```

#### 压缩方式

发送的所有数据的压缩方式：none(默认), gzip, snappy, lz4, zstd。
```
 compression.type=lz4
```

#### 其他
```
 enable.idempotence=true                      # 默认开启幂等性
 bootstrap.servers=node1:9092,node2:9092,...  # 并非需要所有的broker地址，生产者可以从给定的 broker 里查找到其他 broker 信息
```

### Consumer

#### 设置每次消费最大记录数

默认值为 500 条。
```
 Properties props = new Properties();
 props.put("max.poll.records", N);
 ...
 consumer = new KafkaConsumer(props);
```

props 配置变化，需要 new consumer。

当 N 较大时，需要设置 fetch.max.bytes(默认值 52428800，单位 byte，下同)，该参数与 fetch.min.bytes（默认值 1）参数对应，用来配置 Consumer 在一次请求中从 Kafka 中拉取的多批总数据量大小。若一批次的数据大于该值，仍然可以拉取。

#### 获取消费记录

直到取到 max.poll.records 条记录或超过指定时长（如下面语句为 1000 毫秒）。
```
 KafkaConsumer consumer;
 consumer.poll(Duration.ofMillis(1000));
```

### 常见问题
- Cannot invoke "org.apache.kafka.clients.consumer.OffsetAndMetadata.offset()" because "committed" is null
```
 在下列语句中可能会遇到此类问题，原因是该 topic & group 从未被消费过。
 OffsetAndMetadata committed = consumer.committed(topicAndPartition);
 commitOffsetMap.put(topicAndPartition.partition(), committed.offset());
 需要判断 committed==null，committed.offset()替换为零。
```

### Sample

k1.getStream(topic1, group1, N) # 流 -> ConsumerRecords cr

| No | Method | Explain | Example |
|:---|:---|:---|:---|
| + |
| KAFKA | 定义 kafka，默认从 db.cnf 中取 default | KAFKA k1 = new KAFKA("kafkap182", "db.cnf") |
| 1 | reset | 重设消费位置 | k1.reset("test", "test", N)   # 从N开始消费 |
| 2 | get | 获取记录 | k1.get(topic1, group1)       # 默认条数，500 |
| 3 | put | 生产记录 | k1.put("test", ldat1)    # ArrayList ldat1 |
| 4 | getTopics | 获取集群上 topic 列表 | ArrayList l1 = k1.getTopics() |
| 5 | getos | begin, end, offset | k1.getos(topic1, group1)     # [0, 16, 8] |

### GIT

https://github.com/ldscfe/devudefj2/blob/main/src/main/com/udf/KAFKA.java
