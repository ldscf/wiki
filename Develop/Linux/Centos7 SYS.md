---
source_title: Centos7 SYS
categories:
- Develop
- Linux
last_modified: '2023-01-19T12:19:55Z'
---
### 随机数 random
- /dev/random 阻塞的随机数发生器，读取有时需要等待。存储着系统当前运行环境的实时数据，如 CPU、内存、电压、物理信号等，下同
- /dev/urandom 非阻塞的随机数发生器，读取操作不会产生阻塞。
- 如果你不确定该用 /dev/random 还是 /dev/urandom ，那你可能应该用后者。通常来说，除了需要长期使用的 GPG/SSL/SSH 密钥以外，你总该使用/dev/urandom 。
```
 head -2 /dev/urandom | cksum | cut -f1 -d " "
```

### dirty
- Linux 操作系统中的 vm.dirty_background_ratio 参数用来指定当脏页数量达到系统内存的百分之多少之后就会触发 pdflush/flush/kdmflush 等后台回写进程的运行来处理脏页，一般设置为小于10的值即可，但不建议设置为0。
- 与这个参数对应的还有一个 vm.dirty_ratio 参数，它用来指定当脏页数量达到系统内存的百分之多少之后就不得不开始对脏页进行处理，在此过程中，新的 I/O 请求会被阻挡直至所有脏页被冲刷到磁盘中。

### overcommit_memory
- 0， 表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。
- 1， 表示内核允许分配所有的物理内存，而不管当前的内存状态如何。
- 2， 表示内核允许分配超过所有物理内存和交换空间总和的内存

### Overcommit和OOM

# Linux对大部分申请内存的请求都回复“yes”，以便能跑更多更大的程序。因为申请内存后，并不会马上使用内存。这种技术叫做 Overcommit。当linux发现内存不足时，会发生OOM killer(OOM=out-of-memory)。它会选择杀死一些进程(用户态进程，不是内核线程)，以便释放内存。
1. 当oom-killer发生时，linux会选择杀死哪些进程？选择进程的函数是oom_badness函数(在mm/oom_kill.c中)，该 函数会计算每个进程的点数(0~1000)。点数越高，这个进程越有可能被杀死。每个进程的点数跟oom_score_adj有关，而且 oom_score_adj可以被设置(-1000最低，1000最高)。

### /etc/sysctl.conf
```
 kernel.sysrq = 0               ------------------是否启用kernel.sysrq（在大多数服务已无法 
                                                  响应的情况下，还能通过按键组合来完成一系列 
                                                  预先定义的系统操作,1启用，0禁用;
 kernel.core_uses_pid = 1       ------------------可以控制core文件的文件名中是否添加pid作为扩 
                                                  展(文件内容为1，表示添加pid作为扩展名，生成 
                                                  的core文件格式为core.xxxx；为0则表示生成的 
                                                  core文件同一命名为core);
 kernel.msgmnb  = 65536         ------------------限制一个队列的最大长度;
 kernel.msgmax  = 65536         ------------------限制一条消息的最大长度;
 kernel.shmmax  = 68719476736   ------------------最大共享内存段大小;
 kernel.shmall  = 4294967296    ------------------可以使用的共享内存的总量;
 kernel.shmmni  = 4096          ------------------整个系统共享内存段的最大数目;
 kernel.sem     = 250 32000 100 128 --------------每个信号对象集的最大信号对象数；
                                                  系统范围内最大信号对象数；
                                                  每个信号对象支持的最大操数； 
                                                  系统范围内最大信号对象集数;
 kernel.msgmni  = 256           ------------------决定了系统中同时运行的最大的消息队列的个数;
 fs.file-max    = 65536         ------------------系统级打开最大文件句柄的数量;
```
  
```
 net.ipv4.ip_forward     = 0    ------------------(0代表禁止进行IP转发；1代表可以进行IP转发);
 net.ipv4.conf.default.rp_filter = 1  ------------控制系统是否开启对数据包源地址的校验;
 net.ipv4.conf.default.accept_source_route = 0  --禁用icmp源路由选项;
 net.ipv4.tcp_syncookies = 1    ------------------表示开启SYN Cookies。当出现SYN等待队列溢出 
                                                  时，启用cookies来处理，可防范少量SYN攻击， 
                                                  默认为0，表示关闭;
 net.ipv4.conf.all.forwarding = 0  ---------------0代表不转发源路由帧，若做NAT建议开启;
 net.ipv4.ip_local_port_range = 9000 65000  ------系统中的程序会选择这个范围内的端口来连接到目 
                                                  的端口（目的端口当然是用户指定的;
 net.ipv4.tcp_fin_timeout = 30  ------------------表示如果套接字由本端要求关闭，这个参数决定了 
                                                  它保持在FIN-WAIT-2状态的时间,默认是60，降 
                                                  低这个值以提高系统性能;
 net.ipv4.tcp_max_syn_backlog = 8192  ------------定义backlog队列容纳的最大半连接数，如果配置 
                                                  高可以设置的更高;
 net.core.rmem_default   =   262144   ------------套接字接收缓冲区大小的缺省值;
 net.core.rmem_max       =   4194304  ------------套接字接收缓冲区大小的最大值;
 net.core.wmem_default   =   262144   ------------套接字发送缓冲区大小的缺省值;
 net.core.wmem_max       =   262144   ------------套接字发送缓冲区大小的最大值;
```
 
```
 # 修改后配置文件使之生效的办法：
 sysctl -p
```
 
 
```
 # 内存64G，=262144报错：sysctl: setting key "net.core.somaxconn": Invalid argument
 sysctl -w net.core.somaxconn=32768
```
 
```
 # 查看页大小
 getconf PAGE_SIZE
```
 
```
 # Oracle
 shmmax 设置应该足够大，能在一个共享内存段下容纳下整个的SGA，如：256G，SGA通常超过120G，此时 kernel.shmmax = 68719476736 只有64G
 kernel.shmmax = 137438953472
```
