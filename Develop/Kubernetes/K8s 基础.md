---
source_title: K8s 基础
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2025-07-08T07:20:52Z'
---
### 基本概念

#### Pod

Pod 是 K8s 中集群部署应用和服务的最小单元，一个 Pod 中可以部署多个容器。

Pod 的设计理念是支持多个容器在一个 Pod 中共享网络地址和文件系统，可以通过进程间通信和文件共享这种简单高效的方式组合完成服务。Pod 对多容器的支持是 K8s 最基础的设计理念。
- Pod 是 Kubernetes 中运行容器化应用程序的基本单元
- 每个 Pod 都包含一个或多个容器，这些容器共享网络和本地存储卷
- Pod 通常是短暂的，它们可能会被创建、调度、销毁，因此不应依赖于它们的持久存在
- Pod 通常用于临时任务或测试目的

#### RC(副本控制器)

RC(Replication Controller，RC)通过启动/停止 Pod 副本保证集群中有指定数目的 Pod 运行。

#### RS(副本集)

RS(Replica Set，RS)是新一代 RC，提供同样的高可用能力，能支持更多种类的匹配模式。副本集对象一般不单独使用，而是作为 Deployment 的理想状态参数使用。

#### Deployment(部署)

Deployment 提供了一种对 Pod 和 ReplicaSet 的管理方式，官方建议使用Deployment管理ReplicaSets。每一个 Deployment 都对应集群中的一次部署。用来创建/更新/滚动升级服务。

滚动升级实际是创建一个新的 RS，然后逐渐将新 RS 中副本数增加到理想状态，将旧 RS 中的副本数减小到 0 的复合操作。
- Deployment 是用于管理 Pod 的控制器
- Deployment 负责创建、更新和删除 Pod 以达到期望状态
- Deployment 通常用于创建和管理长期运行的应用程序
- Deployment 提供以下功能：
  - 声明式更新: Deployment 允许您以声明式方式定义 Pod 的期望状态，然后 Deployment 控制器会负责实现该状态
  - 自我修复: Deployment 控制器会监视 Pod 的运行状况，并会在 Pod 失败或终止时自动创建新的 Pod 以替换它们
  - 滚动更新: Deployment 可以以滚动的方式更新 Pod，这意味着新版本 Pod 会逐步替换旧版本 Pod，而不会导致应用程序服务中断

#### StatefulSet

StatefulSet 有唯一的网络标识符（IP），持久存储，有序的部署、扩展、删除和滚动更新，适合持久性的应用程序。

#### Service(服务)

RC、RS 和 Deployment 保证了支撑服务的微服务 Pod 的数量，Service 解决如何访问这些服务，以及对绑定的 Pod 提供负载均衡。

一个 Pod 只是一个运行服务的实例，随时可能在节点上停止，然后在新的节点上用一个新的 IP 启动一个新的 Pod，因此不能使用确定的 IP 和端口号提供服务。这对于业务来说，就不能根据 Pod 的 IP 作为业务调度。K8s 就引入了 Service 的概念，为 Pod 提供一个入口，通过 Labels 标签来选择后端的 Pod，从而允许后端 Pod 的 IP 地址变更。

当声明 Service 的时候，会自动生成一个虚拟的 cluster IP，可以通过这个 IP 来访问后端的 Pod。如果集群配置了 CoreDNS 服务，那么也可以通过 Service 的名字来访问。

##### Service 对外暴露服务的方式
1. ClusterIP (默认) ：在集群的内部 IP 上公开 Service 。这种类型使得 Service 只能从集群内访问，一般这种类型的 Service 上层会挂一个 Ingress，通过 Ingress 暴露服务
1. NodePort：在每个选定 Node 的相同端口上公开 Service，使用 : 即可从集群外部访问 Service
1. LoadBalancer：使用云厂商的 K8S 集群，即可使用这种方式的暴露 Service，自建的服务一般不支持。使用 LoadBalancer，会生成一个 IP 地址，通过这个即可访问 Service, 通知这个 IP 也是高可用的
1. ExternalName: 通过返回带有该名称的 CNAME 记录，使用任意名称(由 spec 中的 externalName 指定)公开 Service，不使用代理。这种类型需要 kube-dns v1.7 或更高版本

##### NodePort 端口

NodePort 出来的端口在主机上并不可见(netstat -lntp)，而是通过 iptables (or IPVS)控制，iptables -L 可以看到所有规则。

##### 有头服务、无头服务

有头服务（Headed Service）和无头服务（Headless Service）是针对 Service 类型的一种分类，主要区别在于：是否为服务提供一个集中的访问入口（Cluster IP）。

**有头服务**：默认的 Service，即 ClusterIP 类型的服务，会为一组 Pod 分配一个虚拟 IP（Cluster IP），客户端通过该 IP 访问时，会由 kube-proxy 自动做 负载均衡，请求随机落到某个后端 Pod 上。
- 自动分配 Cluster IP
- kube-dns 返回的是该 IP
- 具备负载均衡能力
- 客户端无法直接感知后端 Pod 详情

**无头服务：**通过将 clusterIP: None 设置在 Service 中，使其成为无头服务。此时，Kubernetes 不再为服务分配 Cluster IP，DNS 查询将直接返回所有匹配 Pod 的 IP 地址。
- 无 Cluster IP（无法通过 Service 进行负载均衡），搭配 StatefulSet 保证 Pod DNS 名称稳定
- kube-dns 返回所有 Pod 的 A 记录
- 客户端可以感知所有 Pod IP，实现自定义的负载均衡或直连
- 常用于有状态服务（StatefulSet）、数据库集群等（如 Zookeeper、Redis Cluster、Cassandra、Kafka）

| 特性 | 有头服务（Headed Service） | 无头服务（Headless Service） |
|:---|:---|:---|
| clusterIP | 自动分配 IP（默认） | None |
| DNS 查询 | 返回一个虚拟 IP | 返回所有 pod 的真实 IP 地址列表 |
| 负载均衡 | kube-proxy | 无负载均衡，需客户端处理 |
| 使用场景 | Web/API 等无状态应用 | Stateful 应用、需指定节点通信等场景 |
| 是否依赖 kube-proxy | 是 | 否 |
| 示例 | my-svc.default.svc.cluster.local | pod-0.my-svc.default.svc.cluster.local |

##### 使用无头服务连接 Redis Cluster

如果部署 Redis Cluster、Kafka、Etcd 等分布式系统，可以使用无头服务配合 StatefulSet，将每个 Pod 的真实 IP 暴露出来，客户端可自行感知并连接特定节点，实现可控的节点发现与连接。如：
 ```
$ dig A redis-cluster-headless.default.svc.cluster.local
;; ANSWER SECTION:
redis-cluster-headless.default.svc.cluster.local. 30 IN A 10.244.0.12
redis-cluster-headless.default.svc.cluster.local. 30 IN A 10.244.0.13
redis-cluster-headless.default.svc.cluster.local. 30 IN A 10.244.0.14
```

#### DaemonSet

DaemonSet 一般是用来部署一些特殊应用的，譬如日志应用等有“守护”意义的应用。

DaemonSet 确保所有（或一些）节点运行同一个Pod。当节点加入 Kubernetes 集群中，Pod 会被调度到该节点上运行，当节点从集群中移除时，DaemonSet 的 Pod 会被删除，删除 DaemonSet 会清理它所有创建的 Pod。

#### Job

一次性任务，运行完成后 Pod 销毁，不再重新启动新容器

#### CronJob

在 Job 基础上加上了定时功能

### 操作

#### 查询
 ```

# -o wide: 显示较多的信息

# -o yaml: 显示全部详细信息

# namespace, pods, service, configmap, deployments, jobs, replicasets, statefulsets, rs, rc
kubectl get ns
kubectl get deploy -n ${NS}
kubectl get pods -n ${NS}
kubectl get svc -n ${NS}
kubectl get cm -n ${NS}

# describe

# pods 在哪些主机上
kubectl describe pods -n ${NS} |grep Node

# 各主机 describe

# 如主机 NotReady，查询原因
kubectl describe node ${NS}

# log
kubectl logs  -n ${NS} [-c ]
```

#### 在容器中执行命令
```
 Format: kubectl exec [POD] -- [COMMAND] instead
```
- 命名空间：-n OR --namespace
- 指定容器：-c OR --container，Pod 中有多个容器
- 交互模式：-it，通常与 shell 命令一起使用，如 bash
```
 -- bash
 kubectl exec  -n ${NS} -- bash
 -- cmd
 kubectl exec  -n ${NS} -- cat /var/log/nginx/access.log
```

#### 滚动重启

在同 node 上将原来的 pod 改名，建立一个新的 pod，与原来同名。
```
 kubectl rollout restart deployment  -n ${NS}
 <<# kubectl get deployment -n ${NS}
 # Starting
 NAME         READY   UP-TO-DATE   AVAILABLE   AGE
 nginx-test   4/3     1            4           92m
 # finished
 NAME         READY   UP-TO-DATE   AVAILABLE   AGE
 nginx-test   3/3     3            3           92m
```
 
```
 原来的 pod 名称 = nginx-test-666988cd9c-qcvnb，rollout restart 过程将原 pod 改名为 nginx-test-7f86f678c6-5x99l。如果 mount 了以 pod 名称的 node 目录，则原目录文件在改名后的目录中。
 # nginx-test-666988cd9c-qcvnb/error.log 
 2024/06/19 07:44:52 [notice] 1#1: using the "epoll" event method
 2024/06/19 07:44:52 [notice] 1#1: nginx/1.27.0
 2024/06/19 07:44:52 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
 2024/06/19 07:44:52 [notice] 1#1: OS: Linux 3.10.0-1127.el7.x86_64
 2024/06/19 07:44:52 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:4096
 2024/06/19 07:44:52 [notice] 1#1: start worker processes
 2024/06/19 07:44:52 [notice] 1#1: start worker process 29
 2024/06/19 07:44:52 [notice] 1#1: start worker process 30
```
 
```
 # nginx-test-7f86f678c6-5x99l/error.log 
 2024/06/19 07:28:29 [notice] 1#1: using the "epoll" event method
 2024/06/19 07:28:29 [notice] 1#1: nginx/1.27.0
 2024/06/19 07:28:29 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
 2024/06/19 07:28:29 [notice] 1#1: OS: Linux 3.10.0-1127.el7.x86_64
 2024/06/19 07:28:29 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1024:4096
 2024/06/19 07:28:29 [notice] 1#1: start worker processes
 2024/06/19 07:28:29 [notice] 1#1: start worker process 29
 2024/06/19 07:28:29 [notice] 1#1: start worker process 30
 2024/06/19 07:44:52 [notice] 1#1: signal 3 (SIGQUIT) received, shutting down
 2024/06/19 07:44:52 [notice] 29#29: gracefully shutting down
 2024/06/19 07:44:52 [notice] 29#29: exiting
 2024/06/19 07:44:52 [notice] 29#29: exit
 2024/06/19 07:44:52 [notice] 30#30: gracefully shutting down
 2024/06/19 07:44:52 [notice] 30#30: exiting
 2024/06/19 07:44:52 [notice] 30#30: exit
 2024/06/19 07:44:52 [notice] 1#1: signal 17 (SIGCHLD) received from 29
 2024/06/19 07:44:52 [notice] 1#1: worker process 29 exited with code 0
 2024/06/19 07:44:52 [notice] 1#1: signal 29 (SIGIO) received
 2024/06/19 07:44:52 [notice] 1#1: signal 17 (SIGCHLD) received from 30
 2024/06/19 07:44:52 [notice] 1#1: worker process 30 exited with code 0
 2024/06/19 07:44:52 [notice] 1#1: exit
```

#### 多个 POD 挂载不同挂载卷
- 使用 statefulset 的 volumeClaimTemplate
  - 需要使用 storageClassName
- 加载环境变量挂载 subpath
  - 创建环境变量 valueFrom/fieldRef
  - 挂载 subPathExpr
```
 env:
 - name: POD_NAME
```

   valueFrom:

     fieldRef:

       fieldPath: metadata.name
```
 ...
 subPathExpr: $(POD_NAME)
```

#### 挂载 NFS
```
 volumes:
 - name: data
```

   nfs:

     path: 

     server: 

#### 弹性伸缩
```
 kubectl scale --replicas=2 deployment  -n ${NS}
 # 也可以直接修改 nginx.yaml -> replicas: 2
 kubectl apply -f nginx.yaml
```

#### 删除
 ```

# 删除 namespace，相当于删除该 namespace 下所有的资源
kubectl delete namespace ${NS}

# 删除 statefulsets

# 有些使用 statefulsets 为 Pod 提供持久存储和持久标识符，先删除 tatefulsets，才可以继续删除相应的 pods
kubectl delete statefulsets --all -n ${NS}

# 强制删除
kubectl delete pod -n ${POD} -n ${NS} --force --grace-period=0

# namespace 无法删除
一般出现此种情况大多为删除操作不规范，如：删除的先后顺序有问题。
调用 Kubernetes API 删除：
1. 获取要删除 NameSpace 的 JSON 文件
  kubectl get ns ${NS} -o json > del_namespace.json
2. 从 finalizers 字段中删除 kubernetes 的值
  "finalizers": []
3. 设置 APIServer 的临时 IP 和端口
  kubectl proxy --port=21312 &
4. API 调用来强制删除
  curl -k -H "Content-Type: application/json" -X  PUT --data-binary @del_namespace.json http://127.0.0.1:21312/api/v1/namespaces/${NS}/finalize

# /run/containerd/io.containerd.runtime.v2.task/k8s.io
该目录是 containerd 容器运行时用来存储容器运行时数据的一个重要目录，包含了容器的层、镜像、配置文件等信息。当容器被删除时，理论上这些数据也应该被清理。但是，由于各种原因，这个目录可能会积累大量无用的数据，从而占用过多的磁盘空间。
find . -maxdepth 1 -mtime +7 -type d
```

### 创建

#### pod
```
 apiVersion: v1
 kind: Pod
 metadata:
```

   name: 
```
 spec:
```

   containers:
   - name: private-reg-container

     image: 
   

#### secret
```
 kubectl create secret docker-registry ${regcred} -n ${NS} --docker-server=${SVR} --docker-username=${USER} --docker-password=${PW}
 # 查询
 kubectl get secret -n $NS
 # 显示原文
 kubectl get secret ${.dockerconfigjson} -o yaml
 kubectl get secret ${.dockerconfigjson} -o jsonpath='{.data}'
 echo ${PS} | base64 --decode
```

### Demo

#### 创建 Nginx 集群
- 采用 {{.Values.metadata.namespace}}
- 在 创建 POD 的 NODE 上，创建并挂载目录 /u01/kube/logs/。fieldRef 也可以指定 apiVersion: v1
- 在所有的 K8s 集群 NODE 上，端口 32080 均可查看
```
 kubectl apply -f nginx.yaml
 kubectl apply -f nginx_s.yaml
```
```
 <<# nginx.yaml
 apiVersion: apps/v1
 kind: Deployment
 metadata:
```

   labels:

     app: nginx

   name: nginx-test

   namespace: rds2024test
```
 spec:
```

   replicas: 3

   selector:

     matchLabels:

       app: nginx

   template:

     metadata:

       labels:

         app: nginx

     spec:

       containers:
       - image: nginx

         ports:
           - containerPort: 80

         name: nginx

         env:
         - name: POD_NAME

           valueFrom:

             fieldRef:

               fieldPath: metadata.name

         volumeMounts:
         - name: log

           mountPath: /var/log/nginx

           subPathExpr: $(POD_NAME)

       volumes:
       - name: log

         hostPath:

           path: /u01/kube/logs

           type: DirectoryOrCreate

   # nginx_s.yaml
```
 apiVersion: v1
 kind: Service
 metadata:
```

   name: nginx-svc

   labels:

     app: nginx

   namespace: rds2024test
```
 spec:
```

   type: NodePort

   selector:

     app: nginx

   ports:
   - port: 80

     targetPort: 80

     nodePort: 32080

### Utility

#### 镜像拉取策略

spec.template.spec.containers.imagePullPolicy
- Always
- IfNotPresent

#### 终止等待时长

spec.template.spec.affinity.terminationGracePeriodSeconds=30

### Error

#### 创建三个 pods，但只有一个成功
```
 可能的原因是只有一个节点上有镜像文件(kubectl apply -f nginx.yaml )
```

#### unknown field "spec.template.spec.name"
```
 yaml 缩进问题
```

#### delete Terminating namespace
```
 kubectl proxy
 kubectl get ns ${NS} -o json > nsdel.json
 vi nsdel.json
 curl -k -H "Content-Type: application/json" -X PUT --data-binary @nsdel.json http://127.0.0.1:8001/api/v1/namespaces/${NS}/finalize
 kubectl delete ns ${NS}
 kubectl delete ns ${NS} --force --grace-period=0
```

#### Unable to retrieve some image pull secrets

imagePullSecret 资源将 Secret 提供的密码传递给 kubelet 从而在拉取镜像前完成必要的认证过程(需要认证的私有镜像仓库)
1. 创建 docker-registry 类型的 Secret 对象，并在定义 pod 资源时明确通过 imagePullSecrets 字段来申明使用哪个私钥去认证
1. 创建 docker-registry 类型的 Secret 对象，然后把它添加到某个 ServiceAccount 对象中，使用了这个 ServiceAccount 对象创建出来的 pod 就自然而然通过认证获取到镜像

#### NFS

mount: wrong fs type, bad option, bad superblock on, missing codepage or helper program, or other error
```
 volumes:
```
   - name: data

     nfs:

       path: /home/nfs/disk1/rds2024dev

       server: 192.168.0.90
- 客户端需要安装相关包
```
 rpc-bind nfs-utils
```
- NFS Server 需要创建相关目录
```
 # 存放当前可用目录，NFS 一般只能对访问端的 IP 配置权限访问
 /etc/exports
```

#### 访问不到其他命名空间的资源

使用 资源名称.命名空间名称 访问
