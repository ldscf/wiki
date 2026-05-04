---
source_title: K8s 资源
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2024-07-08T06:21:33Z'
---
Kubernetes 中的所有内容都被抽象为“资源”，如 Pod、Service、Node 等都是资源。“对象”就是“资源”的实例，是持久化的实体，通过这些实体显示整个集群的状态。

对象的创建、删除、修改都是通过 “Kubernetes API”，也就是 “API Server” 组件提供的 API 接口，这些是 RESTful 风格的 API，与 K8s 的“万物皆对象”理念相符。命令行工具 “kubectl”，实际上也是调用 Kubernetes API。

K8s 中的资源类别有很多种，kubectl 可以通过配置文件来创建这些 “对象”，配置文件更像是描述对象“属性”的文件，配置文件格式可以是 “JSON” 或 “YAML”，常用 “YAML”。

#### 公共部分
- apiVersion: 创建该对象所使用的 Kubernetes API 的版本
- kind: 想要创建的对象的类型
- metadata: 帮助识别对象唯一性的数据，包括一个 name 字符串、UID 和可选的 namespace

#### 规约(spec)和状态(status)

对象是用来完成一些任务的，是持久的，是有目的性的，因此 Kubernetes 创建一个对象后，将持续地工作以确保对象存在。每个 Kubernetes 对象包含两个嵌套的对象字段，它们负责管理对象的配置，他们分别是 “spec” 和 “status” 。
- spec 描述了对象的期望状态（Desired State）—— 希望对象所具有的特征。当创建 Kubernetes 对象时，必须提供对象的规约，用来描述该对象的期望状态，以及关于对象的一些基本信息（例如名称）
- status 描述了对象的实际状态（Actual State） ，它是由 Kubernetes 系统提供和更新的

下面的配置文件描述了一个对象，对象的类型是 “Pod”，对象名为 “myapp-pod”，包含一个 “app: myapp” 标签。“spec” 指定了该 Pod 对象的特征：包含一个名为 “myapp-container” 的容器，容器根据 “busybox” 镜像生成，容器运行的命令是 “ ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600'] ”。
```
 apiVersion: v1
 kind: Pod
 metadata:
   name: myapp-pod
   labels:
     app: myapp
 spec:
   containers:
   - name: myapp-container
     image: busybox
     command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
```

#### 常用属性

##### name & uid

K8s 中每个对象都有 uid，uid 是 K8s 自动为对象生成的，可以唯一标识该对象的字符串。对象还具有另一个属性 name。在创建对象时可以给对象指定名称。不属于同一类别的对象可以有相同的名称，而同一种类型的对象，想要赋予相同的名称，则需要用到 namespace。

##### namespace

K8s 中， namespace (命名空间) 也称为虚拟集群。namespace 用来在逻辑上对资源进行分组。K8s 会创建 "default" 的 namespace，供未明确指定资源时使用。kubernetes 的组件，分配的是名为 “kube-system” 的 namespace。
```
 # 列出所有命名空间
 kubectl get namespace
```
 
```
 # 列出资源及缩写:
 kubectl api-resources --namespaced=true
 true : 在命名空间中
 false: 不归属于命名空间
```

##### label & selector

label（标签）是附加到 Kubernetes 对象（如 Pods）上的键值对，用于区分对象（如 Pod、Service）。 label 旨在用于指定对用户有意义且相关的对象的标识属性，但不直接对核心系统有语义含义。 label 可以用于组织和选择对象的子集。label 可以在创建时附加到对象，随后可以随时添加和修改。可以像 namespace 一样，使用 label 来获取某类对象，但 label 可以与 selector 一起配合使用，用表达式对条件加以限制，实现更精确、更灵活的资源查找。

label 与 selector 配合，可以实现对象的“关联”，“Pod 控制器” 与 Pod 是相关联的 —— “Pod 控制器”依赖于 Pod，可以给 Pod 设置 label，然后给“控制器”设置对应的 selector，这就实现了对象的关联。

每个对象都可以定义一组键/值标签。每个键对于给定对象必须是唯一的。

#### Persistent Volumes

##### 持久卷（PersistentVolume，PV）

是集群中的一块存储，可以由管理员事先制备， 或者使用存储类（Storage Class）来动态制备。持久卷是集群资源，就像节点也是集群资源一样。PV 持久卷和普通的 Volume 一样，也是使用卷插件来实现的，只是它们拥有独立于任何使用 PV 的 Pod 的生命周期。

##### 持久卷申领（PersistentVolumeClaim，PVC）

表达的是用户对存储的请求。概念上与 Pod 类似。 Pod 会耗用节点资源，而 PVC 申领会耗用 PV 资源。Pod 可以请求特定数量的资源（CPU 和内存）。同样 PVC 申领也可以请求特定的大小和访问模式 （例如，可以挂载为 ReadWriteOnce、ReadOnlyMany、ReadWriteMany 或 ReadWriteOncePod， 请参阅访问模式）。

##### 存储类（StorageClass）

尽管 PVC 允许用户消耗抽象的存储资源， 常见的情况是针对不同的问题用户需要的是具有不同属性（如，性能）的 PV 卷。 集群管理员需要能够提供不同性质的 PV， 并且这些 PV 卷之间的差别不仅限于卷大小和访问模式，同时又不能将卷是如何实现的这些细节暴露给用户。 为了满足这类需求，就有了存储类（StorageClass，SC） 资源。

##### 静态制备

集群管理员创建若干 PV 卷。这些卷对象带有真实存储的细节信息， 并且对集群用户可用（可见）。PV 卷对象存在于 Kubernetes API 中，可供用户消费（使用）。

##### 动态制备

如果管理员所创建的所有静态 PV 卷都无法与用户的 PersistentVolumeClaim 匹配， 集群可以尝试为该 PVC 申领动态制备一个存储卷。 这一制备操作是基于 StorageClass 来实现的：PVC 申领必须请求某个存储类， 同时集群管理员必须已经创建并配置了该类，这样动态制备卷的动作才会发生。 如果 PVC 申领指定存储类为 ""，则相当于为自身禁止使用动态制备的卷。

##### 存储能力（Capacity）

描述存储设备具备的能力，支持对存储空间的设置（storage=xx）

##### 存储卷模式（Volume Mode）

volumeMode=xx，可选项包括 Filesystem（文件系统）和 Block（块设备），默认值是 FileSystem。

目前 PV 类型支持块设备类型：AWSElasticBlockStore、AzureDisk、FC、GCEPersistentDisk、iSCSI、Local volume、NFS、RBD（Ceph Block Device）、VsphereVolume（alpha）等

##### 访问模式（Access Modes）

用于描述应用对存储资源的访问权限
- ReadWriteOnce（RWO）：读写权限，并且只能被单个Node挂载
- ReadOnlyMany（ROX）：只读权限，允许被多个Node挂载
- ReadWriteMany（RWX）：读写权限，允许被多个Node挂载

##### 存储类别（Class）

设定存储的类别，通过 storageClassName 参数指定给一个 StorageClass 资源对象的名称，具有特定类别的PV只能与请求了该类别的 PVC 进行绑定。未绑定类别的 PV 则只能与不请求任何类别的PVC进行绑定。

##### 回收策略（Reclaim Policy）

通过 persistentVolumeReclaimPolicy 字段设置
- Retain 保留：保留数据，需要手工处理
- Recycle 回收空间：简单清除文件的操作（例如执行 rm -rf /thevolume/* 命令）
- Delete 删除：与PV相连的后端存储完成 Volume 的删除操作

##### Sample

###### local
```
 *# pv - task-pv-volume
 apiVersion: v1
 kind: PersistentVolume
 metadata:
   name: task-pv-volume
   labels:
     type: local
 spec:
   storageClassName: manual
   capacity:
     storage: 1Gi
   accessModes:
     - ReadWriteOnce
   hostPath:
     path: "/mnt/data"
```

 
```
 # pvc - task-pv-claim
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
   name: task-pv-claim
 spec:
   storageClassName: manual
   accessModes:
     - ReadWriteOnce
   resources:
     requests:
       storage: 1Gi
```

 
```
 # POD
 ...
 containers:
   - name: rc
     imagePullPolicy: IfNotPresent
     image: rc:2.1.1
     volumeMounts:
       - name: data
         mountPath: /opt/rc/data
 volumes:
   - name: data
     persistentVolumeClaim:
       claimName: task-pv-claim
 ...*
```

###### NFS
```
 *apiVersion: v1
 kind: PersistentVolume
 metadata:
   name: pv-test1
 spec:
   storageClassName: slow
   capacity:
     storage: 2Gi
   volumeMode: Filesystem
   accessModes:
     - ReadWriteOnce
   nfs:
     path: /home/nfs/disk1/rds2024dev4
     server: 192.168.0.90
```

 
```
 ---
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
   name: pv-claim-test1
 spec:
   storageClassName: slow
   accessModes:
     - ReadWriteOnce
   resources:
     requests:
       storage: 1Gi*
```

###### 指定 PV

PVC 可以使用 storageClassName 指定连接的 PV。

如：下列 yaml 中指定 pv-b1-claim 连接 storageClassName = pv-b 中符合条件的 Volume，但因 pv-b1 大小问题，而连接不上。
```
 *apiVersion: v1
 kind: PersistentVolume
 metadata:
   name: pv-a1
 spec:
   storageClassName: pv-a
   capacity:
     storage: 2Gi
   accessModes:
     - ReadWriteOnce
   hostPath:
     path: "/mnt/data/pva"
     type: DirectoryOrCreate
```

 
```
 ---
 apiVersion: v1
 kind: PersistentVolume
 metadata:
   name: pv-b1
 spec:
   storageClassName: pv-b
   capacity:
     storage: 1500Mi
   accessModes:
     - ReadWriteOnce
   hostPath:
     path: "/mnt/data/pvb"
     type: DirectoryOrCreate
```

 
```
 ---
 apiVersion: v1
 kind: PersistentVolumeClaim
 metadata:
   name: pv-b1-claim
 spec:
   accessModes:
     - ReadWriteOnce
   storageClassName: pv-b
   resources:
     requests:
       storage: 2Gi*
```
