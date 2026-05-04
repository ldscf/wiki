---
source_title: Network traffic capture
categories:
- Develop
- Linux
last_modified: '2024-09-13T08:49:35Z'
---
网络数据采集分析工具(network traffic capture & packet analyzer)，俗称抓包工具。比较常用的有 wireshark(图形化界面)，tcpdump(命令行)。

### tcpdump

[TcpDump](https://www.tcpdump.org), a powerful command-line packet analyzer; and libpcap, a portable C/C++ library for network traffic capture.

#### INST
```
 apt install tcpdump
```

#### Usage
```
 tcpdump -i ens3 port 10010
 tcpdump -i ens3 -X port 10010
```
