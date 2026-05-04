---
source_title: Hadoop install
categories:
- Develop
- Hadoop
last_modified: '2024-12-23T06:25:43Z'
---
### ENV

#### USER
```
 groupadd hadoop -g 1001
 useradd hdfs -g hadoop -u 1001
```

#### Java
```
 ln -s /usr/java/jdk1.8.0_361/jre/bin/java /usr/bin/java
```

# Java Error

java 以前使用 --version 来查看版本的；java8 以后变更为 -version。
```
 $ java --version
 Unrecognized option: --version
 Error: Could not create the Java Virtual Machine.
 Error: A fatal exception has occurred. Program will exit.
```
```
 $  java -version
 java version "1.8.0_361"
 Java(TM) SE Runtime Environment (build 1.8.0_361-b09)
 Java HotSpot(TM) 64-Bit Server VM (build 25.361-b09, mixed mode)
```

#### hadoop
```
 ln -s /opt/hadoop-3.3.0 /opt/hadoop
```

#### profile
```
 # Java, 20201010, Adam
 export JAVA_HOME=/usr/java/jdk1.8.0_361
 export PATH=$PATH:$JAVA_HOME/bin
 # hadoop, 20201010, Adam
 export HADOOP_HOME=/opt/hadoop
 export PATH=$PATH:$HADOOP_HOME/bin 
 export PATH=$PATH:$HADOOP_HOME/sbin
 export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop
```

### Hadoop 配置

#### 配置 Hadoop 环境脚本文件中的 JAVA_HOME 参数

# hadoop是守护线程 读取不到 /etc/profile 里面配置的JAVA_HOME路径
```
 # /opt/hadoop/etc/hadoop/
 # hadoop-env.sh, mapred-env.sh, yarn-env.sh
 cp hadoop-env.sh hadoop-env.sh.20210409
 cp mapred-env.sh mapred-env.sh.20210409
 cp yarn-env.sh   yarn-env.sh.20210409
```
 
```
 echo '
 # hdfs, 20210409, Adam
 export JAVA_HOME=/usr/java/jdk1.8.0_361' >> 
```

#### Setup

##### core-site.xml (Common组件)
```
 
   
     <!-- 配置hdfs地址 -->
     fs.defaultFS
     hdfs://**g2-hdfs-01:9000**
   
   
     io.file.buffer.size
     131072
   
   
         <!-- 保存临时文件目录 -->
     hadoop.tmp.dir
     /u01/hdfs/tmp
   
 
```

##### hdfs-site.xml (HDFS组件)
- 修改 dfs.replication 不影响历史文件的备份数。修改历史文件备份数：hadoop fs -setrep -R 3 
```
 
   
     <!-- 主节点地址 -->
     dfs.namenode.http-address
     **g2-hdfs-01:9870**
   
   
         <!-- 第二节点地址 -->
     dfs.namenode.secondary.http-address
     **g2-hdfs-02:9870**
   
   
     dfs.namenode.name.dir
     file:/u01/hdfs/dfs/nn
   
   
     dfs.datanode.data.dir
     file:/u01/hdfs/dfs/dn
   
    
     dfs.webhdfs.enabled 
     true 
   
   
         <!-- 配置false后，无需权限即可生成dfs上的文件 -->
     dfs.permissions
     false
   
   
             <!-- 在文件中出现的主机会被下线 -->
     dfs.hosts.exclude
     /opt/hadoop/etc/hadoop/workers.exclude
   
 
```
 
```
 -- del
   
         <!-- 备份数为默认值3 -->
     dfs.replication
     3
   
   
     dfs.blocksize
     268435456
   
   
     dfs.namenode.handler.count
     100
   
```

##### mapred-site.xml
```
 
   
     mapreduce.framework.name
     yarn 
   
 
```
 
```
 -- del
   
      mapreduce.jobhistory.address
      g2-hdfs-01:10020
   
   
      mapreduce.jobhistory.webapp.address
      g2-hdfs-01:19888
   
   
      mapreduce.application.classpath
      $HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/ hadoop/mapreduce/lib/*
   
```

##### yarn-site.xml
```
 
   
     yarn.resourcemanager.hostname
     **g2-hdfs-01**
   
   
     yarn.nodemanager.aux-services  
     mapreduce_shuffle
   
   
     yarn.resourcemanager.webapp.address
     **g2-hdfs-01:8088**
   
   
     yarn.scheduler.maximum-allocation-mb
     32768
   
   
     yarn.nodemanager.vmem-check-enabled
     false
   
   
     yarn.nodemanager.env-whitelist
     JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPE ND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME
   
 
```
```
   
     yarn.resourcemanager.webapp.address
     hadoop01/192.168.44.5:8088
     配置外网只需要替换外网ip为真实ip，否则默认为 localhost:8088
   
```
 
```
 yarn.resourcemanager.hostname
 指定yarn的ResourceManager管理界面的地址，不配的话，Active Node始终为0
 yarn.scheduler.maximum-allocation-mb
 每个节点可用内存,单位MB,默认8182MB
 yarn.nodemanager.aux-services
 reducer获取数据的方式
 yarn.nodemanager.vmem-check-enabled
 false = 忽略虚拟内存的检查
```

##### workers
```
 g2-hdfs-01
 g2-hdfs-02
 ..
```

#### Node

##### Status
```
 hdfs dfsadmin -report
 yarn node -list
```

##### Add

新增节点上启动 dfs & yarn 服务后，namenode 节点：
- 可看到新增节点
- workers 需增加新增节点主机名称，否则下次 start-dfs.sh 会漏掉该节点

##### Exclude

若在 hdfs-site.xml 中配置了 dfs.hosts.exclude，则可以动态下线主机。
```
 hdfs dfsadmin -refreshNodes
```

注意：hdfs-site.xml 内容修改，需重启 dfs 生效。但配置了 dfs.hosts.exclude，修改相应文件后 refresh node 即可生效。

### INIT
```
 # 每个节点
 # Path
 mkdir -p /u01/hdfs/dfs/nn
 mkdir -p /u01/hdfs/dfs/dn
 mkdir -p /u01/hdfs/tmp
```
 
```
 # chown
 chown -R hdfs:hadoop /opt/hadoop*
 chown -R hdfs:hadoop /u01/hdfs
```
 
```
 # format nn
 /opt/hadoop/bin/hdfs namenode -format
```

### Start
```
 ## primary
 ## Start : hdfs(需要用 hdfs 停服务，root 不可停）
 /opt/hadoop/sbin/start-dfs.sh
 /opt/hadoop/sbin/start-yarn.sh
```
 
```
 /opt/hadoop/sbin/stop-yarn.sh
 /opt/hadoop/sbin/stop-dfs.sh
```
 
```
 http://mc0:9870   # hdfs
 http://mc0:8088   # yarn
```
```
 ## 单节点启停
 # /opt/hadoop/bin
 hdfs --daemon start datanode
 hdfs --daemon start namenode
```
 
```
 yarn --daemon start resourcemanager 
 yarn --daemon start nodemanager
```

### SYNC
```
 # HN=$1
 HN=g2-hdfs-02
```
 
```
 # hosts
 ssh ${HN} "hostnamectl set-hostname ${HN}"
 scp /etc/hosts ${HN}:/etc/hosts
```
 
```
 # Java
 scp /u01/soft/jdk_180361.tar.gz ${HN}:/tmp/
 ssh ${HN} "cd /tmp;tar -xzvf jdk_180361.tar.gz;mkdir /usr/java/;mv jdk1.8.0_361 /usr/java/;"
 ssh ${HN} "ln -s /usr/java/jdk1.8.0_361/jre/bin/java /usr/bin/java"
```
 
```
 # Hadoop
 ssh ${HN} "groupadd hadoop -g 1001;useradd hdfs -m -s /bin/bash -g hadoop -u 1001"
```
 
```
 scp /home/hdfs/.ssh/authorized_keys ${HN}:/tmp/
 ssh ${HN} "mkdir /home/hdfs/.ssh;mv /tmp/authorized_keys /home/hdfs/.ssh/;chown -R hdfs:hadoop /home/hdfs/.ssh"
```
 
```
 scp /u01/soft/hadoop_334.tar.gz ${HN}:/tmp/
 ssh ${HN} "cd /tmp;tar -xzvf hadoop_334.tar.gz;mv hadoop-3.3.4 /opt/;ln -s /opt/hadoop-3.3.4 /opt/hadoop;chown -R hdfs:hadoop /opt/hadoop*"
```
 
```
 ssh ${HN} "mkdir -p /u01/hdfs/dfs/nn /u01/hdfs/dfs/dn /u01/hdfs/tmp;chown -R hdfs:hadoop /u01/hdfs"
 ssh hdfs@${HN} "/opt/hadoop/bin/hdfs namenode -format"
```

### Error

#### 主机名称不合法

ERROR org.apache.hadoop.hdfs.server.namenode.NameNode: Failed to start namenode.java.lang.IllegalArgumentException: Does not contain a valid host:port authority: esxi_mc0:9000

主机名称不合法，不允许包含‘.’ ‘/’ '_'等非法字符

#### NameNode addresses

WARN org.apache.hadoop.hdfs.server.datanode.DataNode: Unable to get NameNode addresses

检查 core-site.xml 中 fs.defaultFS 配置

#### 退出安全模式
```
 hdfs dfsadmin -safemode leave
```

#### 启动后无资源

多次格式化 namenode 导致系统 namenode 和 DataNode 的 cluster ID 不匹配

初始化 /opt/hadoop/bin/hdfs namenode -format 时，将目标目录清空。或将 name/data node 的 VERSION 中的 clusterID 统一。
