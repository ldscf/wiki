---
source_title: Greenplum安装
categories:
- DB
- Develop
- Greenplum
last_modified: '2022-12-29T08:17:43Z'
---
Greenplum 安装、配置

## INIT

### Check
```
  ./rrun ip_gp 'sestatus'
  ./rrun ip_gp 'cat /etc/
  ./rrun ip_gp 'systemctl
  ./rrun ip_gp 'hostname'
```

### sestatus
```
  SELinux status: disabled
  Disabling SELinux and Firewall Software
```
```
  SELinux 临时关闭（不一定管用，P.S. 根本不管用。SELinux只能重启;好像管用）
  setenforce 0
```
```
  vi /etc/selinux/config
  SELINUX=disabled
```

### firewalld
```
  systemctl status firewalld
  systemctl stop firewalld
  systemctl disable firewalld
```

### Set the required operating system parameters
```
  The hosts File
```

### The sysctl.conf File
```
  Set the parameters in the /etc/sysctl.conf file and reload with sysctl -p
  echo $(expr $(getconf _PHYS_PAGES) / 2) 
  echo $(expr $(getconf _PHYS_PAGES) / 2 \* $(getconf PAGE_SIZE))
  kernel.shmall = _PHYS_PAGES / 2 # See Shared Memory Pages
  kernel.shmmax = kernel.shmall * PAGE_SIZE 
```
```
  vm.overcommit_memory = 2 # See Segment Host Memory
  vm.overcommit_ratio = 80 # See Segment Host Memory
```

### >= 64G
```
  vm.dirty_background_ratio = 0 # See System Memory
  vm.dirty_ratio = 0
  vm.dirty_background_bytes = 1610612736 ＃1.5GB
  vm.dirty_bytes = 4294967296 ＃4GB
```

### < 64G
```
  vm.dirty_background_ratio = 3
  vm.dirty_ratio = 10
```

20210916, Adam, Greenplum 6.17
- shmall, shmmax 根据内存调整
  - 64G=8172895,67456868352
- 256G=32920550,134842572800
- sysctl设置不正确，会造成部分节点生成不成功    # 2022/6/15

### sysctl.conf
```
  kernel.shmall = 32920550
  kernel.shmmax = 134842572800
  kernel.shmmni = 4096
  vm.overcommit_memory = 2
  vm.overcommit_ratio = 80
  net.ipv4.ip_local_port_range = 10000 65535
  kernel.sem = 500 2048000 200 4096
  kernel.sysrq = 1
  kernel.core_uses_pid = 1
  kernel.msgmnb = 65536
  kernel.msgmax = 65536
  kernel.msgmni = 2048
  net.ipv4.tcp_syncookies = 1
  net.ipv4.conf.default.accept_source_route = 0
  net.ipv4.tcp_max_syn_backlog = 4096
  net.ipv4.conf.all.arp_filter = 1
  net.core.netdev_max_backlog = 10000
  net.core.rmem_max = 2097152
  net.core.wmem_max = 2097152
  vm.swappiness = 10
  vm.zone_reclaim_mode = 0
  vm.dirty_expire_centisecs = 500
  vm.dirty_writeback_centisecs = 100
  vm.dirty_background_ratio = 0
  vm.dirty_ratio = 0
  vm.dirty_background_bytes = 1610612736
  vm.dirty_bytes = 4294967296
```

### System Resources Limits
1. soft表示软限制；hard表示硬限制；nproc进程数；nofile文件数
1. Set the following parameters in the /etc/security/limits.conf file:
1. vi /etc/security/limits.d/20-nproc.conf

20210916, Adam, Greenplum 6.17
- soft nofile 524288
- hard nofile 524288
- soft nproc 131072
- hard nproc 131072

./lrun ip_gp 'scp gp/sysctl.conf %VAR%:/etc/.' 

./rrun ip_gp 'sysctl -p'

./lrun ip_gp 'scp gp/20-nproc.conf %VAR%:/etc/security/limits.d/20-nproc.conf'

### Disk I/O Settings
```
  ./rrun ip_gp '/sbin/blockdev --setra 16384 /dev/sda
  # /sbin/blockdev --getra /dev/sda
  DEV=/dev/mapper/centos-home
  DEV=/dev/sdb
  IP=10.10.139.12
  ssh $IP "/sbin/blockdev --setra 16384 ${DEV}"
  ssh $IP "/sbin/blockdev --getra ${DEV}"
```
 
```
  vi /etc/rc.d/rc.local
  ulimit -SHn 131072
```

### Disk I/O scheduler
1. CentOS 7.x默认支持的就是deadline算法
1. CentOS 6.x下默认支持的cfq算法，而一般我们会在SSD固态盘硬盘环境中使用noop算法
1. echo deadline > /sys/block/sda/queue/scheduler
1. 永久生效的方法 CentOS 7.x

#grubby --update-kernel=ALL --args="elevator=deadline"
```
 echo never > /sys/kernel/mm/transparent_hugepage/enabled
 echo never > /sys/kernel/mm/transparent_hugepage/defrag
```

### Transparent Huge Pages (THP) 在上面 Disk I/O
```
 # cat /sys/kernel/mm/transparent_hugepage/enabled
 # echo never > /sys/kernel/mm/transparent_hugepage/enabled
 always [never]
 always madvise [never]
```

### IPC Object Removal
```
 Disable RemoveIPC. Set this parameter in /etc/systemd/logind.conf on the Greenplum Database host systems.
 RemoveIPC=no
 # service systemd-logind restart
 # systemctl restart systemd-logind
```

### SSH Connection Threshold
```
 # vi /etc/ssh/sshd_config
 #MaxStartups 10:30:100
 #MaxSessions 10
 #20210916, Adam, Greenplum 6.17
 MaxStartups 10:30:200
 MaxSessions 200
 # systemctl restart sshd
```

### Synchronizing System Clocks

### XFS Mount Options
```
 XFS is the preferred data storage file system on Linux platforms. Use the mount command with the following  recommended XFS mount options for RHEL and CentOS systems:
 rw,nodev,noatime,nobarrier,inode64
```

### Creating the Greenplum Administrative User
```
 # groupadd gpadmin
 # useradd gpadmin -u 2200 -r -m -g gpadmin
 # passwd gpadmin    #Bigp.28
 # groupadd gpadmin;useradd gpadmin -u 2200 -r -m -g gpadmin;passwd gpadmin
```

### run visudo and uncomment the %wheel group entry
```
 %wheel        ALL=(ALL)       NOPASSWD: ALL
 Make sure you uncomment the line that has the NOPASSWD keyword.
 Add the gpadmin user to the wheel group with this command.
 # usermod -aG wheel gpadmin
```
```
 ./rrun ip_gp 'mkdir /u01/gpdb/;chown gpadmin:gpadmin -R /u01/gpdb'
```

### .bash_profile
```
 #20220927, Adam, Greenplum 6.22
 export GPHOME=/usr/local/greenplum-db
 export PATH=$GPHOME/bin:$PATH
 export LD_LIBRARY_PATH=$GPHOME/lib
 export MASTER_DATA_DIRECTORY=/u01/gpdb/master/gpseg-1
```

## Installing Greenplum Database
```
 # wget http://10.10.137.16/soft/linux/greenplum/open-source-greenplum-db-6.22.0-rhel7-x86_64.rpm
 # sudo yum install open…
 # sudo chown -R gpadmin:gpadmin /usr/local/greenplum*
```
 
```
 ## batch
 ./rrun ip_owgp 'cd /tmp;yum -y install open….rpm;chown -R gpadmin:gpadmin /usr/local/greenplum*'
```
 
```
 ./rrun ip_gp 'wget http://10.10.137.16/soft/linux/greenplum/open-source-greenplum-db-6.22.0-rhel7-x86_64.rpm; yum -y  install open-source-greenplum-db-6.22.0-rhel7-x86_64.rpm;chown -R gpadmin:gpadmin /usr/local/greenplum-db*'
```
 
 
```
 # 在一台主机上(一般为管理主机）ssh-keygen，然后设定可以ssh所有机器
 ssh-keygen
```
 
```
 ## Enabling Passwordless SSH
```
 
```
 # 主机互信（在可以ssh所有主机的机器上执行）
 # hostfile_exkeys
 owgpp
 owgpm
 owgpd3
 owgpd4
```
 
```
 # 环境变量
 source /usr/local/greenplum-db/greenplum_path.sh
```
 
```
 gpssh-exkeys -f hostfile_exkeys
```
 
 
```
 ./rrun ip_gp 'mkdir /home/gpadmin/.ssh;chown -R gpadmin:gpadmin /home/gpadmin/.ssh'
 ./lrun ip_gp 'scp gp/grant/* %VAR%:/home/gpadmin/.ssh/'
 ./rrun ip_gp 'chown -R gpadmin:gpadmin /home/gpadmin/.ssh/'
 ./lrun ip_gp 'scp gp/grant/known_hosts %VAR%:/home/gpadmin/.ssh/'
 ./rrun ip_gp 'chown -R gpadmin:gpadmin /home/gpadmin/.ssh/'
```

### 建立数据库目录
```
 # /u01
 #    gpdb/master
 #        /primary
 #        /mirror
 ./rrun ip_gp_137 'mkdir -p /u01/gpdb;cd /u01/gpdb;mkdir master primary mirror;chown gpadmin:gpadmin -R /u01/gpdb'
```

### 在primary上执行
```
 # Creating the Greenplum Database Configuration File
 mkdir /home/gpadmin/gpconfigs/
 cp $GPHOME/docs/cli_help/gpconfigs/gpinitsystem_config /home/gpadmin/gpconfigs/gpinitsystem_config
 cd /home/gpadmin/gpconfigs/
 # vi gpinitsystem_config 
 ARRAY_NAME="BIGP"
 PORT_BASE=21000
 declare -a DATA_DIRECTORY=(/u01/gpdb/primary /u01/gpdb/primary /u01/gpdb/primary /u01/gpdb/primary /u01/gpdb/primary  /u01/gpdb/primary /u01/gpdb/primary /u01/gpdb/primary)
 MASTER_HOSTNAME=bimd1
 MASTER_DIRECTORY=/u01/gpdb/master
 # Mirror
 MIRROR_PORT_BASE=21100
 declare -a MIRROR_DATA_DIRECTORY=(/u01/gpdb/mirror /u01/gpdb/mirror /u01/gpdb/mirror /u01/gpdb/mirror /u01/gpdb/ mirror /u01/gpdb/mirror /u01/gpdb/mirror /u01/gpdb/mirror)
 # DB
 DATABASE_NAME=cbsgp
 MACHINE_LIST_FILE=/home/gpadmin/gpconfigs/hostfile_gpinitsystem
```
 
```
 MASTER_MAX_CONNECT=300        # 不易过大，默认250。(256G内存 --> 2000报错，1000 OK)
```
 
```
 ## Install
 # gpinitsystem -c gpconfigs/gpinitsystem_config -h gpconfigs/hostfile_gpinitsystem
 # 结果见 gpinitsystem 
```
 
 
```
 # 访问权限
 # /u01/gpdb/master/gpseg-1/pg_hba.conf
 # pg_hba.conf
 host    all    all    10.0.0.0/8  md5
```
 
```
 # 设置Greenplum数据库时区
 # 需要 gpstop -u
 # gpconfig -s TimeZone
 # gpconfig -s log_statement
 gpconfig -c TimeZone -v 'Asia/Shanghai'
 gpconfig -c log_statement -v none -m ddl
```
 
```
 # 以上参数生效
 gpstop -u
```
 
 
 
```
 # 建立冗余的master节点
 # master 节点pg_hba.conf修改后，standby节点也需要同步修改。目标目录不能存在
 gpinitstandby -s n09 -S /u01/gpdb/standby/gpseg-1 [-P 5432]
```

## Check
```
 # Check 在primary上执行，测试所有主机读写，-r (d)磁盘IO swim——时间较长，没必要做
 # 会在指定目录下建立 gpcheckperf_gpadmin 目录，写入 ddfile
 # -rw-rw-r-- 1 gpadmin gpadmin 488447627264 Mar 22 16:47 ddfile
 # gpcheckperf -f hostfile_gpcheckperf -r sM -D -d /u01/gpdb
 # 
 # gpcheckperf -f hostfile_exkeys -r N -d /tmp
 ====================
 ==  RESULT 2020-10-01T17:50:56.683977 (105.*)
 ====================
 Netperf bisection bandwidth test
 etlgpp -> etlgpm = 113.340000
 etlgpd1 -> etlgpd2 = 112.340000
 etlgpm -> etlgpp = 113.340000
 etlgpd2 -> etlgpd1 = 112.340000
```
 
```
 Summary:
 sum = 451.36 MB/sec
 min = 112.34 MB/sec
 max = 113.34 MB/sec
 avg = 112.84 MB/sec
 median = 113.34 MB/sec
```
 
 
 
```
 ====================
 ==  RESULT 2020-09-25T05:40:25.786123 (Oracle Cloud)
 ====================
 Netperf bisection bandwidth test
 gpbi-p -> gpbi-m = 489.650000
 gpbi-3 -> gpbi-4 = 475.920000
 gpbi-m -> gpbi-p = 475.900000
 gpbi-4 -> gpbi-3 = 476.110000
```
 
```
 Summary:
 sum = 1917.58 MB/sec
 min = 475.90 MB/sec
 max = 489.65 MB/sec
 avg = 479.39 MB/sec
 median = 476.11 MB/sec
```
 
```
 ====================
 ==  RESULT 2020-08-05T13:12:43.987676 (137.*)
 ====================
 Netperf bisection bandwidth test
 m01 -> n07 = 106.710000
 n08 -> n09 = 112.330000
 n07 -> m01 = 91.640000
 n09 -> n08 = 112.280000
```
 
```
 Summary:
 sum = 422.96 MB/sec
 min = 91.64 MB/sec
 max = 112.33 MB/sec
 avg = 105.74 MB/sec
 median = 112.28 MB/sec
```
