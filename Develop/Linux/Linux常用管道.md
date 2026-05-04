---
source_title: Linux常用管道
categories:
- Develop
- Linux
last_modified: '2025-06-20T02:26:00Z'
---
管道是 Linux 中很重要的一种通信方式，是把一个程序的输出直接连接到另一个程序的输入，通常说的管道多是指无名管道。有名管道叫 named pipe 或者 FIFO(先进先出)，可以用函数 mkfifo() 创建。

**管道的结构**: 在 Linux 中，管道的实现并没有使用专门的数据结构，而是借助了文件系统的 file 结构和 VFS 的索引节点 inode。通过将两个 file 结构指向同一个临时的 VFS 索引节点，而这个 VFS 索引节点又指向一个物理页面而实现的。

#### gzip
```
 gzip -dc filename.gz | wc -l
```

#### unzip
```
 unzip -cq ${HN} '*.csv' | wc -l
```

#### tar
- 解决打包同一批文件 hash 不同问题
```
 tar -c pangolin | gzip -n > pangolin.tar.gz
 tar -cvf - pangolin |gzip -n > pangolin.tar.gz
```

第一行使用了 tar 的默认行为：如果没有指定 -f 文件名，会将归档内容写入标准输出。而第二行显式地使用 -f - 可以保证行为一致：明确地将输出发送到标准输出。
- 不指定解压文件或目录，则输出全部
```
 hadoop fs -cat /bakdata/gio/visit/visit_202102.tar.gz | tar -xzv 20210201.csv
```
