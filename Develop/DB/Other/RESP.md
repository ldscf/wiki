---
source_title: RESP
categories:
- DB
- Develop
- OtherDB
- Redis
last_modified: '2025-06-06T01:54:29Z'
---
RESP (REdis Serialization Protocol) 是 Redis 客户端与服务器之间进行通信的序列化协议。是一个相对简单、易于实现、且易于人阅读的文本协议。

### Network layer

A client connects to a Redis server by creating a TCP connection to its port (the default is 6379).

客户端和服务器发送的命令或数据以 \r\n （CRLF）结尾。

### 工作方式

#### 发送

客户端将要执行的 Redis 命令及其参数封装成一个 RESP 数组。例如：set a 123:
```
 *3\r\n
 $3\r\nSET\r\n
 $1\r\a\r\n
 $3\r\123\r\n
```

#### 回复

返回类型可以是简单字符串 (例如 "OK")，错误 (例如 "ERR")，整数 (例如 `INCR` 的结果)，批量字符串 (例如 `GET` 的结果)，或数组 (例如 `LRANGE` 的结果)

### 数据类型

| 数据类型 | 前缀 | 格式 | 用途 | 示例 |
|:---|:---|:---|:---|:---|
| + |
| 简单字符串 | + | +OK\r\n | 表示简单的、单行、非二进制字符串 |
| 错误 | - | -Error message\r\n | 表示服务器发生的错误 | -ERR unknown command 'sit'\r\n |
| 整数 | : | :n\r\n | :1000\r\n |
| 批量字符串 | $ | $n\r\ndata\r\n | 表示二进制安全的字符串，可以包含特殊字符 | $6\r\n1 2 ,3\r\n |
| 数组 | * | *N\r\n$n1\r\n\r\n... | 表示一个由其他 RESP 类型组成的列表 | *2\r\n$3\r\nget\r\n$1\r\n\a\r\n |

### Redis traffic capture

$3

get

$1

a

1

$3 get $1 d

jdkd a 234 1ka fslakdf s

$3 get $1 e

$3 set $1 b $3 234

$3 set $1 c $8 1 2 ,3  

$3 get

$3 cls

$4 quit

$7

COMMAND

$4

DOCS
```
  2)  1) "summary"
```
```
      2) "Returns the expiration time of a key as a Unix milliseconds timestamp."
```
```
      3) "since"
```
```
      4) "7.0.0"
```
```
      5) "group"
```
```
      6) "generic"
```
```
      7) "complexity"
```
```
      8) "O(1)"
```
```
      9) "arguments"
```
```
     10) 1) 1) "name"
```
```
            2) "key"
```
```
            3) "type"
```

...

$11

pexpiretime

*10

$7

summary

$70

Returns the expiration time of a key as a Unix milliseconds timestamp.

$5

since

$5

7.0.0

$5

group

$7

generic

$10

complexity

$4

O(1)

$9

arguments

*1

*8

$4

name

$3

key

$4

type

...

INFO SERVER

COMMAND

(Many, many, much more)

16400 bytes is not enough

| rowspan="2" | Type | colspan="2" | Client | colspan="2" | Server | rowspan="2" | Memo |
| Original | Send | Display | Receive |
| rowspan="3" | get | get a | *2 | "1" | $1 |
| get d | *2 | "jdkd a 234 1ka fslakdf s" | $24 |
| get e | *2 | (nil) | $-1 |
| rowspan="2" | set | set b 234 | *3 | OK | +OK |
| set c "1 2 ,3  " | *3 | OK | +OK |
| rowspan="2" | Error | get | *1 | -ERR wrong number of arguments for 'get' command | -ERR wrong number of arguments for 'get' command |
| cls | *1 | -ERR unknown command 'cls', with args beginning with: | -ERR unknown command 'cls', with args beginning with: |
| rowspan="2" | exit | exit | 由客户端处理，直接退出。 |
| quit | *1 | OK | +OK | 服务端主动退出。 |
| (connect) | *2 | 1) "pexpiretime" | *796 | V7版本客户端连接时，发送的第一个指令。更早的版本，发送： |
