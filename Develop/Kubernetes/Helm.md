---
source_title: Helm
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2024-07-17T02:53:58Z'
---
Helm 是 Kubernetes 的包管理器，可以帮助部署和管理应用程序。
1. Chart 代表着 Helm 包。它包含在 Kubernetes 集群内部运行应用程序，工具或服务所需的所有资源定义。你可以把它看作是 Homebrew formula，Apt dpkg，或 Yum RPM 在 Kubernetes 中的等价物(就是一组 yaml 文件，Chart 含有配置、拉取镜像地址等，并不含有镜像实体)。
1. Repository（仓库） 是用来存放和共享 charts 的地方。它就像 Perl 的 CPAN 档案库网络 或是 Fedora 的 软件包仓库，只不过它是供 Kubernetes 包所使用的。
1. Release 是运行在 Kubernetes 集群中的 chart 的实例。一个 chart 通常可以在同一个集群中安装多次。每一次安装都会创建一个新的 release。以 MySQL chart为例，如果你想在你的集群中运行两个数据库，你可以安装该 chart 两次。每一个数据库都会拥有它自己的 release 和 release name。

### INST

https://github.com/helm/helm/releases

helm -> /usr/local/bin/

版本 V2 与 V3 语法不同。

### Chart 仓库
```
 # env
 export KUBECONFIG=/root/.kube/config
 echo "KUBECONFIG=/root/.kube/config" >> /etc/profile
 source /etc/profile
```
```
 # 添加官方稳定仓库
 helm repo add stable https://charts.helm.sh/stable
 # 添加开源仓库
 helm repo add bitnami https://charts.bitnami.com/bitnami
 # 添加官方测试仓库
 helm repo add incubator https://charts.helm.sh/incubator
 # 删除
 helm repo remove incubator
```
 
```
 # 查看已添加的仓库列表
 helm repo list
 # 更新仓库本地缓存
 helm repo update
 # 搜索 charts
 helm search repo nginx
 # 安装 charts
 helm install mynginx stable/nginx
```

### CMD

#### 创建
- helm search
  - hub $chart_name  # 从 Artifact Hub 中查找公开可用的 charts。Artifact Hub 中存放了大量不同的仓库
  - repo $chart_name # 从到本地仓库（使用 helm repo add）中进行查找。该命令基于本地数据进行搜索，无需连接互联网
- helm create $chart_name   # 创建 chart
- helm lint         # 验证格式是否正确
- helm package      # chart 打包分发
- helm status $release_name      # 追踪 release 状态
- helm show values               # 查看 chart 中的可配置选项
- helm install 
  - $release_name $chart_name                      # 安装 helm 包
  - helm install $release_name $chart_name.tgz     # 安装本地 helm 包
  - helm upgrade $release_name $chart_name.tgz     # 更新本地 helm 包
  - 使用 YAML 格式的文件覆盖上述任意配置项
```
 helm show values bitnami/wordpress
 echo '{mariadb.auth.database: user0db, mariadb.auth.username: user0}' > values.yaml
 helm install -f values.yaml bitnami/wordpress --generate-name
 --values/-f values.yaml   # 使用 values.yaml 中的值替换原配置相应的值
 --set a=b,c=d             # helm get values  来查看指定 release 中 --set 设置的值
 --generate-name           # 让 Helm 生成一个名称
```

#### 使用

helm -n ns
- ls -a
- get manifest $release_name

### Sample

#### Create
```
 helm create testvol
```
- Chart.yaml   # 定义包内容
- values.yaml  # template 中 yaml 使用的变量
- templates    # yaml 文件

#### Package
```
 helm package testvol
```

#### install
```
 NS='-n test2024'
 # helm ${NS} ls
 # helm ${NS} delete $release_name
 helm ${NS} install $release_name
```
 ```

## Chart.yaml
apiVersion: v2
name: testvol
description: A Helm chart for test Volumn

## values.yaml - add
namespace: test2024dev
rdspv:
  metadata:
    name: rdspv
  spec:
    # storageClassName: manual
    capacity:
      storage: 1Gi
    accessModes:
      - ReadWriteOnce
    hostPath:
      path: "/mnt/data"
rdspvc:
  metadata:
    name: rdspv-claim

## pvc.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <!-- template:  .Values.rdspv.metadata.name  -->-<!-- template:  .Values.namespace  -->
spec:
  storageClassName: sc-<!-- template:  .Values.namespace  -->
  capacity:
    storage: <!-- template:  .Values.rdspv.spec.capacity.storage  -->
  accessModes:
    <!-- template:  .Values.rdspv.spec.accessModes  -->
  hostPath:
    path: <!-- template:  .Values.rdspv.spec.hostPath  -->
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <!-- template:  .Values.rdspv.metadata.name  -->-<!-- template:  .Values.namespace  -->
spec:
  storageClassName: sc-<!-- template:  .Values.namespace  -->
  accessModes:
    <!-- template:  .Values.rdspv.spec.accessModes  -->
  resources:
    requests:
      storage: <!-- template:  .Values.rdspv.spec.capacity.storage  -->

## pod.yaml
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: <!-- template:  .Values.rdspv.metadata.name  -->-<!-- template:  .Values.namespace  -->
```

### Error
- Error: INSTALLATION FAILED: cannot re-use a name that is still in use
```
 有安装失败的服务占用了名称
 helm -n ${NS} ls -a
 helm -n ${NS} delete $release_name
```
