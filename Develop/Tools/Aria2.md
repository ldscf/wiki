---
source_title: Aria2
categories:
- Doc
- Tools
last_modified: '2025-05-29T01:46:20Z'
---
Aria2 是一款开源下载工具，有着优秀的性能及较低的资源占用。

支持磁力链接、BT 种子、http 等类型的文件下载。

#### Install
```
 apt install aria2
```

#### Example
```
 aria2c ???.torrent
 aria2c magnet:?xt=???
```

#### Option
- 下载路径
```
 -d /tmp
```
- 限速
```
 --max-download-limit=300K
```
- 多线程
```
 -s10
```
- 分段
```
 -x10
```
