---
source_title: Zombie process
categories:
- Linux
- Ubuntu
last_modified: '2024-06-12T07:00:36Z'
---
On Unix and Unix-like computer operating systems, a zombie process or defunct process is a process that has completed execution (via the exit system call) but still has an entry in the process table: it is a process in the "terminated state". 

在类 UNIX 系统中，僵尸进程是指完成执行（通过 exit 系统调用，或运行时发生致命错误或收到终止信号所致），但在操作系统的进程表中仍然存在其进程控制块，处于"终止状态"的进程。这发生于子进程需要保留表项以允许其父进程读取子进程的退出狀態：一旦退出态通过 wait 系统调用读取，僵尸进程条目就从进程表中删除，称之为"回收"（reaped）。正常情况下，进程直接被其父进程 wait 并由系统回收。进程长时间保持僵尸状态一般是错误的并导致资源泄漏。

#### 查进程
```
 ps aux | grep 'Z'
```
 
```
 USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
 root      670710  0.0  0.0   9144  2048 pts/0    R+   11:20   0:00 grep --color=auto Z
 bi       1672993  0.0  0.0      0     0 ?        Z    May14   0:00 [sd_espeak-ng-mb] 
```
 
```
 pstree -p -s 1672993
 systemd(1)───systemd(1489696)───speech-dispatch(1672981)───sd_espeak-ng-mb(1672993)
```
 
```
 kill 1672981
```
