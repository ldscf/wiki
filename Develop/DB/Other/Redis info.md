---
source_title: Redis info
categories:
- DB
- Develop
- OtherDB
- Redis
last_modified: '2025-04-21T06:30:01Z'
---
Redis Info 命令返回 Redis 服务器的各种信息和统计数值。通过可选的参数 section，可以让命令只返回某一部分的信息。

### Server

Redis 服务器信息，包含 Redis 服务本身的一些信息，例如版本号、运行模式、操作系统的版本、TCP端口等。

| Parameter | Description | Value |
| redis_version | Redis 服务器版本 | 7.2.4 |
| redis_git_sha1 | Git SHA1 | 00000000 |
| redis_git_dirty | Git dirty flag | 0 |
| redis_build_id | 构建 ID | 9b24e1b8ebc9fc59 |
| redis_mode | 服务器模式 (standalone, sentinel, cluster) | standalone |
| os | Redis 服务器的宿主操作系统 | Linux 5.15.0-116-generic x86_64 |
| arch_bits | 架构 （32 或64 位） | 64 |
| monotonic_clock | POSIX clock_gettime |
| multiplexing_api | Redis 所使用的事件处理机制 | epoll |
| atomicvar_api | Redis 使用的Atomicvar API | c11-builtin |
| gcc_version | 编译 Redis 时所使用的 GCC 版本 | 9.4.0 |
| process_id | 服务器进程的 PID | 823652 |
| process_supervised | no |
| run_id | Redis 服务器的随机标识符 (sentinel, cluster) | 92bb5d699d8f8390fa81450e95927f50646ab6df |
| tcp_port | TCP/IP 监听端口 | 6379 |
| server_time_usec | 1722907650249681 |
| uptime_in_seconds | 自 Redis 服务器启动以来，经过的秒数 | 32 |
| uptime_in_days | 自 Redis 服务器启动以来，经过的天数 | 0 |
| hz | serverCron 每秒运行次数 | 10 |
| configured_hz | 10 |
| lru_clock | 以分钟为单位进行自增的时钟，用于 LRU 管理 | 11631618 |
| executable | 服务器的可执行文件路径 | /opt/redis-stack/bin/redis-server |
| config_file | 配置文件路径 | /etc/redis-stack.conf |
| io_threads_active | 0 |
| listener0 | name=tcp,bind=*,bind=-::*,port=6379 |

### Clients

客户端信息，包含了连接数、阻塞命令连接数、输入输出缓冲区等相关统计信息。

| Parameter | Description | Value |
| connected_clients | 已连接客广端的数量（不包括通过从属服务器连接的客户端） | 1 |
| cluster_connections | 0 |
| maxclients | 10000 |
| client_recent_max_input_buffer | 当前连接的客户端当中，最大输入缓存 | 20480 |
| client_recent_max_output_buffer | 当前连接的客户端当中，最长的输出列表 | 0 |
| blocked_clients | 正在等待阻塞命令 (BLPOP、BRPOP、BRPOPLPUSH) 的客户端数量 | 0 |
| tracking_clients | 0 |
| clients_in_timeout_table | 0 |
| total_blocking_keys | 0 |
| total_blocking_keys_on_nokey | 0 |
| OLD Version |
| client_longest_output_list | 当前所有输出缓冲区中队列对象个数的最大值 |
| client_biggest_input_buf | 当前所有输人缓冲区中占用的最大容量 |

### Memory

内存信息，包含了Redis内存使用、系统内存使用、碎片率、内存分配器等相关统计信息。

| Parameter | Description | Value |
| used_memory | 自 Redis 分配器分配的内存总量，以字节 (byte) 为单位 | 1386312 |
| used_memory_rss | 从操作系统的角度，返回 Redis 已分配的内存总量，与 top、ps 等命令输出一致 | 22896640 |
| used_memory_peak | Redis 的内存消耗峰值(以字节为单位） | 1579536 |
| used_memory_peak_perc | 使用内存占峰值内存的百分比 | 87.77% |
| used_memory_overhead | 服务器为管理其内部教据结构而分配的所有开销的总和(以字节为单位) | 1215032 |
| used_memory_startup | Redis 在启动时消耗的初始内存大小(以字节为单位) | 1192344 |
| used_memory_dataset | 以字节为单位的数据集大小(= used_memory - used_memory_overhead) | 171280 |
| used_memory_dataset_perc | used_memory_dataset 占净内存使用量的百分比 (= used_memory - used_memory_startup) | 88.30% |
| allocator_allocated | 3872768 |
| allocator_active | 9895936 |
| allocator_resident | 11796480 |
| total_system_memory | Redis 主机具有的内存总量 | 12537851904 |
| used_memory_lua | Lua 引擎所使用的內存大小(以字节为单位) | 31744 |
| used_memory_vm_eval | 31744 |
| used_memory_scripts_eval | 0 |
| number_of_cached_scripts | 0 |
| number_of_functions | 0 |
| number_of_libraries | 0 |
| used_memory_vm_functions | 32768 |
| used_memory_vm_total | 64512 |
| used_memory_functions | 184 |
| used_memory_scripts | 184 |
| maxmemory | 配置设置的最大可使用内存值，默认 0，不限制 | 0 |
| maxmemory_policy | maxmemory-policy 配置指令的值 | noeviction |
| allocator_frag_ratio | 2.56 |
| allocator_frag_bytes | 6023168 |
| allocator_rss_ratio | 1.19 |
| allocator_rss_bytes | 1900544 |
| rss_overhead_ratio | 1.94 |
| rss_overhead_bytes | 11100160 |
| mem_fragmentation_ratio | used_memory_rss 和 used_memory 之间的比率 | 16.55 |
| mem_fragmentation_bytes | 21512744 |
| mem_not_counted_for_evict | 0 |
| mem_replication_backlog | 0 |
| mem_total_replication_buffers | 0 |
| mem_clients_slaves | 0 |
| mem_clients_normal | 22400 |
| mem_cluster_links | 0 |
| mem_aof_buffer | 0 |
| mem_allocator | 在编译时指定的 Redis 所使用的内存分配器。可以是 ibc,  jemalloc, tcmalloc | jemalloc-5.3.0 |
| active_defrag_running | 指示活动碎片整理是否处于洁动状态的标志 | 0 |
| lazyfree_pending_objects | 等待释放的对象数(由于使用 ASYNC 选项调用 UNLINK 或FLUSHDB 和 FLUSHALL) | 0 |
| lazyfreed_objects | 0 |
P.S. XXX**_human**   以人类可读的格式显示（如 1386312，显示：1.32M）

### Persistence

### Stats

| Parameter | Description | Value |
| total_connections_received | 连接过的客户端总数 | 1 |
| total_commands_processed | 执行过的命令总数 | 9 |
| instantaneous_ops_per_sec | 每秒处理命令条数 | 0 |
| total_net_input_bytes | 输人总网络流量(以字节为单位） | 145 |
| total_net_output_bytes | 输出总网络流量(以字节为单位) | 216383 |
| total_net_repl_input_bytes | 0 |
| total_net_repl_output_bytes | 0 |
| instantaneous_input_kbps | 每秒输入字节数 | 0.00 |
| instantaneous_output_kbps | 每秒输出字节数 | 0.00 |
| instantaneous_input_repl_kbps | 0.00 |
| instantaneous_output_repl_kbps | 0.00 |
| rejected_connections | 拒绝的连接个数 | 0 |
| sync_full | 主从完全同步成功次数 | 0 |
| sync_partial_ok | 主从部分同步成功次数 | 0 |
| sync_partial_err | 主从部分同步失败次数 | 0 |
| expired_keys | 过期的 key 数量 | 0 |
| expired_stale_perc | 0.00 |
| expired_time_cap_reached_count | 0 |
| expire_cycle_cpu_milliseconds | 0 |
| evicted_keys | 剔除 (超过了maxmemory 后) 的 key 数量 | 0 |
| evicted_clients | 0 |
| total_eviction_exceeded_time | 0 |
| current_eviction_exceeded_time | 0 |
| keyspace_hits | 命中次数 | 3 |
| keyspace_misses | 不命中次数 | 0 |
| pubsub_channels | 当前使用中的频道数量 | 0 |
| pubsub_patterns | 当前使用中的模式数量 | 0 |
| pubsubshard_channels | 0 |
| latest_fork_usec | 最近一次 fork 操作消耗的时间（微秒） | 0 |
| total_forks | 0 |
| migrate_cached_sockets | 记录当前 Redis 正在进行 migrate 操作的目标 Redis 个数 | 0 |
| slave_expires_tracked_keys | 0 |
| active_defrag_hits | 0 |
| active_defrag_misses | 0 |
| active_defrag_key_hits | 0 |
| active_defrag_key_misses | 0 |
| total_active_defrag_time | 0 |
| current_active_defrag_time | 0 |
| tracking_total_keys | 0 |
| tracking_total_items | 0 |
| tracking_total_prefixes | 0 |
| unexpected_error_replies | 0 |
| total_error_replies | 1 |
| dump_payload_sanitizations | 0 |
| total_reads_processed | 5 |
| total_writes_processed | 6 |
| io_threaded_reads_processed | 0 |
| io_threaded_writes_processed | 0 |
| reply_buffer_shrinks | 1 |
| reply_buffer_expands | 0 |
| eventloop_cycles | 322 |
| eventloop_duration_sum | 35554 |
| eventloop_duration_cmd_sum | 1267 |
| instantaneous_eventloop_cycles_per_sec | 9 |
| instantaneous_eventloop_duration_usec | 105 |
| acl_access_denied_auth | 0 |
| acl_access_denied_cmd | 0 |
| acl_access_denied_key | 0 |
| acl_access_denied_channel | 0 |

### Replication

### CPU

### Modules

### Errorstats

### Cluster

### Keyspace
