---
source_title: K8s 安装
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2026-01-07T06:33:58Z'
---
Kubernetes v1.24+ 已经移除了对 Docker-shim 的支持，故而下面的安装使用 containerd。

### 概述

| No | 步骤 | 说明 | Master | Worker |
|:---|:---|:---|:---|:---|
| + |
| 1 | 环境准备 | 配置系统环境 | ✅ | ✅ |
| 2 | 安装容器运行时 | 安装并配置 containerd 作为 K8s 的容器运行时 | ✅ | ✅ |
| 3 | 安装 K8s 组件 | kubeadm/kubelet/kubectl | ✅ | ✅ |
| 4 | 初始化 Master 节点 | 使用 kubeadm 创建集群的控制平面 | ✅ |
| 5 | 安装网络插件 (CNI) | 使用 Calico 作为网络解决方案 | ✅ |
| 6 | 加入 Worker 节点 | 将工作节点添加到集群中 | ✅ |

### 环境准备
- 关闭 selinux 及 firewalld
- 关闭 Swap

#### host
```
 192.168.0.158   np0
 192.168.0.229   np1
 192.168.0.249   np2
 192.168.0.148   np3
```

#### 设置网桥参数
```
 cat << EOF > /etc/sysctl.d/99-kubernetes-cri.conf
 net.bridge.bridge-nf-call-ip6tables = 1
 net.bridge.bridge-nf-call-iptables = 1
 net.ipv4.ip_forward = 1
 user.max_user_namespaces=28633
 EOF
```
 
```
 sysctl -p /etc/sysctl.d/99-kubernetes-cri.conf
```

#### 配置支持 IPVS

加载 ip_vs 内核模块。kube-proxy 通过采用 iptables + ipset + ipvs 的方式实现为符合条件的 Pod 提供负载均衡。否则 kube-proxy 会退回到 iptables 模式。
```
 cat > /etc/modules-load.d/ip_vs.conf << EOF 
 ip_vs
 ip_vs_rr
 ip_vs_wrr
 ip_vs_sh
 nf_conntrack_ipv4
 EOF
```
```
 modprobe ip_vs
 modprobe ip_vs_rr
 modprobe ip_vs_wrr
 modprobe ip_vs_sh
 modprobe nf_conntrack_ipv4
```

#### 导入模块
```
 cat << EOF > /etc/modules-load.d/containerd.conf
 overlay
 br_netfilter
 EOF
```
```
 modprobe overlay
 modprobe br_netfilter
```
```
 lsmod | grep overlay
 lsmod | grep br_netfilter
```

### 部署 Containerd

#### Yum Inst
 ```

# Repo
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum makecache fast
yum install -y yum-utils device-mapper-persistent-data lvm2
yum install -y containerd.io

# 如果已安装过
kubeadm reset -f
rm -rf /etc/kubernetes /var/lib/etcd /var/lib/kubelet /etc/cni /opt/cni /var/lib/cni /run/flannel /etc/containerd/config.toml /var/lib/containerd $HOME/.kube/config
```

#### 手动 Inst

**创建容器工具**
```
 wget https://github.com/opencontainers/runc/releases/download/v1.1.4/runc.amd64
 install -m 755 runc.amd64 /usr/local/sbin/runc
```

**容器间网络通信**
```
 wget https://github.com/containernetworking/plugins/releases/download/v1.2.0/cni-plugins-linux-amd64-v1.2.0.tgz
 mkdir -p /opt/cni/bin
 tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.2.0.tgz
```

**Containerd**
```
 wget https://github.com/containerd/containerd/releases/download/v1.7.14/containerd-1.7.14-linux-amd64.tar.gz
 tar Cxzvf /usr/local containerd-1.7.14-linux-amd64.tar.gz
```
 
```
 wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -o /usr/lib/systemd/system/containerd.service
 systemctl daemon-reload && systemctl enable containerd
```

#### 配置 containerd
 ```
mkdir /etc/containerd
containerd config default > /etc/containerd/config.toml
cd /etc/containerd/
cp config.toml config.toml.orig
vi config.toml
 [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    #SystemdCgroup = false
    SystemdCgroup = true
 [plugins."io.containerd.grpc.v1.cri"]
    # sandbox_image = "registry.k8s.io/pause:3.9"
    sandbox_image = "registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.9"
systemctl restart containerd
netstat -nlput | grep containerd
```

### kubernetes

#### repo
```
 cat > /etc/yum.repos.d/kubernetes.repo << EOF
 [kubernetes]
 name=Kubernetes
 baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
 enabled=1
 gpgcheck=1
 repo_gpgcheck=1
 gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
 EOF
```

#### kubelet kubeadm kubectl
```
 # yum list kubelet --showduplicates
```
 
```
 yum install kubelet kubeadm kubectl
 systemctl enable kubelet
 systemctl status kubelet
 此时状态不正常，等到 init 或 join 后，状态正常。
```

#### master

##### INIT
```
 kubeadm init \
 --image-repository registry.aliyuncs.com/google_containers \
 --kubernetes-version=1.28.2 \
 --apiserver-advertise-address=192.168.0.249 \
 --service-cidr=10.1.0.0/16 \
 --pod-network-cidr=10.2.0.0/16
```
- apiserver-advertise-address: master 主机 IP 地址
- service-cidr: 内部 service 使用 IP 范围，不可与 pod 及 master 重复
- pod-network-cidr: k8s pod 节点之间网络通信使用 IP 范围，安装 calico 网络插件需要
```
 *```
```

Your Kubernetes control-plane has initialized successfully!
 
```
 To start using your cluster, you need to run the following as a regular user:
```

 
```
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

 
```
 Alternatively, if you are the root user, you can run:
```

 
```
   export KUBECONFIG=/etc/kubernetes/admin.conf
```

 
```
 You should now deploy a pod network to the cluster.
 Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
   https://kubernetes.io/docs/concepts/cluster-administration/addons/
```

 
```
 Then you can join any number of worker nodes by running the following on each as root:
```

 
```
 kubeadm join 192.168.0.249:6443 --token z1q425.gy4kgpp491c8nkq2 --discovery-token-ca-cert-hash sha256:0b02fa4069856afb9d17dba76527b7e7c630d799cc3c00c3cc36c8beaec0128c 
```

```*

根据上面安装完毕后出现的信息，需要如下操作：
 ```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
1. 如果是 root，还需要配置
export KUBECONFIG=/etc/kubernetes/admin.conf
```

配置完毕后，kubectl 命令就可以正常执行了（当然在安装完 calico 网络插件前还是 NotReady 状态）。
```
 kubectl get nodes
```

##### Calico

Calico 是 Tigera 公司主导的 CNI 插件，提供网络通信 + 网络安全策略（NetworkPolicy），支持 BGP、IPIP、VXLAN，适合中大型生产环境。
```
 # 注意：Calico 的版本需要与 Kubernetes 版本大致兼容。如：v3.26.x 与 K8s 1.28 兼容良好。
 wget https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/calico.yaml
 kubectl apply -f calico.yaml
```
```
 # 如果在 kubeadm init 时，改变了 pod-network-cidr，那么在安装 Calico 时，必须也要改 Calico 配置，以确保和 --pod-network-cidr 保持一致
 # calico.yaml
 # - name: CALICO_IPV4POOL_CIDR
 #   value: "192.168.0.0/16"
 -->
 - name: CALICO_IPV4POOL_CIDR
   value: "10.2.0.0/16"
 # 查看 pod-network-cidr
 kubectl get pods -n kube-system -l component=kube-controller-manager -o yaml | grep cluster-cidr
      - --cluster-cidr=10.2.0.0/16
```
```
 # 状态查看：
 watch kubectl get pods -n kube-system
 # 如果出现 calico-node-cwffr  0/1  Init:ImagePullBackOff，说明 Calico 镜像拉取失败。
```

**使用国内镜像替换安装 Calico**
1. 下载官方 Calico 配置文件
```
 curl -O https://raw.githubusercontent.com/projectcalico/calico/v3.26.4/manifests/calico.yaml
```
2. 修改镜像地址为国内镜像
 ```
1. calico.yaml
 # sed -i 's#docker.io/calico#quay.io/calico#g' calico.yaml
 将：
 docker.io/calico/cni
 docker.io/calico/node
 docker.io/calico/kube-controllers
 替换为：
 quay.io/calico/cni
 quay.io/calico/node
 quay.io/calico/kube-controllers
 P.S. registry.aliyuncs.com 不建议使用
```

##### kube-flannel

Flannel 是 CoreOS 开发的轻量级 CNI 插件，提供简单的 Pod 到 Pod 网络通信，适合小型集群，配置简单。Flannel 与 Calico 二选一，不应混合使用。 

Flannel vs. Calico 对比表： 

| 功能/特性 | **Flannel** | **Calico** |
|:---|:---|:---|
| 网络模型 | Layer 3 (Overlay) | Layer 3 (BGP)、Overlay（IPIP/VXLAN） |
| 网络策略支持 | ❌ 不支持 | ✅ 支持 |
| 安装复杂度 | ✅ 非常简单 | ⚠️ 略复杂（但 Helm 支持完善） |
| 性能 | 中等（VXLAN 封装有开销） | 高（支持直连、无封装） |
| 适合场景 | 小规模开发测试环境 | 生产环境、复杂网络场景 |
| 安全策略 | ❌ 无 | ✅ 有 |
| 社区活跃度 | 较低 | 非常活跃（企业支持、Cloud Native） |
```
 wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
 修改 net-conf.json:Network 与 pod-network-cidr 保持一致。
   "Network": "10.2.0.0/16",
```
```
 kubectl apply -f kube-flannel.yml
```

#### Node
```
 kubectl get cs
 kubectl get node
```
```
 kubectl label node ${NODE} node-role.kubernetes.io/worker=worker
 kubectl label node ${NODE} node-role.kubernetes.io/master=master
 kubectl label node ${NODE} node-role.kubernetes.io/worker-
```

#### Node Join
 ```
kubeadm join 192.168.0.249:6443 --token yxxbrx.4i7ojwfjve8wxvf0 \
 	--discovery-token-ca-cert-hash sha256:58e769eb976860038ef7dee524de91286ff36aef20f0109e19056e410ca60004
1. 如果节点执行 kubectl 命令，则需要 master 中的配置文件
**(192.168.0.249) /etc/kubernetes/admin.conf --> (Client) ~/.kube/config**
 

# 若 Client 以前加入过集群
kubeadm reset -f
systemctl restart kubelet

# rm -rf /etc/cni/net.d         # 这个应该用不删除，master 需要

kubeadm join ...

systemctl stop containerd
rm -rf /var/lib/containerd
systemctl start containerd

kubeadm join 的 token 默认 24 小时过期，再有新节点加入需重生成 token。
kubeadm token create --print-join-command

# 指定有效期
kubeadm token create --print-join-command --ttl 2h
```

====Node delete====
```
 # 将节点上的 Pod 安全驱逐（evict），为节点做下线准备
 kubectl drain ${NODE} --delete-local-data --force --ignore-daemonsets
 # 从集群中删除该节点对象
 kubectl delete node ${NODE}
 P.S. 只执行 delete node 而不 drain，Pod 会直接失联（NotFound），这对有状态服务可能是灾难性的。
```


====GUI====
```
 wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
 kubectl apply -f recommended.yaml 
 kubectl proxy --address=0.0.0.0 --port=18001 --accept-hosts='^*$' &
```


```
 http://192.168.0.158:18001/
 http://192.168.0.158:18001/api
 http://192.168.0.158:18001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
```


=====Getting a Bearer Token for ServiceAccount=====

*[https://kubernetes.io/zh-cn/docs/tasks/access-application-cluster/web-ui-dashboard/ 部署和访问 Kubernetes 仪表板（Dashboard）] 

*[https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md Creating sample user]

======Creating a Service Account======
```
 # y1.yaml
 apiVersion: v1
 kind: ServiceAccount
 metadata:
   name: admin-user
   namespace: kubernetes-dashboard
```

====== Creating a ClusterRoleBinding ======
```
 # y2.yaml
 apiVersion: rbac.authorization.k8s.io/v1
 kind: ClusterRoleBinding
 metadata:
   name: admin-user
 roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: cluster-admin
 subjects:
 - kind: ServiceAccount
   name: admin-user
   namespace: kube-system
```

======Creating a token======
```
 kubectl -n kube-system create token admin-user
```


=====Getting a long-lived Bearer Token for ServiceAccount=====
```
 # y3.yaml
 apiVersion: v1
 kind: Secret
 metadata:
   name: admin-user
   namespace: kube-system
   annotations:
     kubernetes.io/service-account.name: "admin-user"   
 type: kubernetes.io/service-account-token  
```

 
```
 kubectl get secret admin-user -n kube-system -o jsonpath={".data.token"} | base64 -d
```


=====Remove the admin ServiceAccount and ClusterRoleBinding=====

kubectl -n kube-system delete serviceaccount admin-user

kubectl -n kube-system delete clusterrolebinding admin-user

===kubelet Guide===

====Create token====
```
 # Expires 24h
 kubeadm token create --print-join-command
```


====kubectl info====
```
 NODE=node3
 # ENV(master): scp /etc/kubernetes/admin.conf ${NODE}:/root/.kube/confi
```


```
 kubectl version --client
 kubectl version --client --output=yaml
 kubectl get apiservices
```

 
```
 kubectl get cs
 kubectl get node
 kubectl describe node
 kubectl describe node nginx-deployment-86dcfdf4c6-5d47c
 kubectl describe deployment nginx-deployment
```

 
```
 kubectl get pods
 kubectl get pods -l app=nginx
 kubectl get pods --all-namespaces
```


====暴露服务====
```
 kubectl get svc
 # kubectl apply -f redis.yaml
 # kubectl delete svc redis-svc
```


=====ClusterIP(集群内)===== 
```
 apiVersion: v1
 kind: Service
 metadata:
   name: redis-svc
 spec:
   selector:
     app: redis-pod
   type: ClusterIP
   ports:
     - protocol: TCP
       port: 6379
       targetPort: 6379
```

 
=====NodePort(集群外) =====
```
 kubectl apply -f redis.yaml
```

 
```
 apiVersion: v1
 kind: Service
 metadata:
   name: redis-svc
 spec:
   selector:
     app: redis-pod
   type: NodePort
   ports:
     - protocol: TCP
       port: 6379
       nodePort: 32379
       targetPort: 6379
```


====pods====
```
 # kubectl apply -f https://k8s.io/examples/application/deployment.yaml
 # wget https://k8s.io/examples/application/deployment.yaml
 kubectl apply -f deployment.yaml
 # kubectl delete deployment nginx-deployment
 # kubectl delete pods nginx-deployment-86dcfdf4c6-5d47c
 -.OR.-
 kubectl run nginx --image=nginx --port=8180
```


===ctr===
```
 ctr version
 ctr namespaces ls
 ctr container list
 ctr images pull docker.io/library/nginx:alpine      # pull 镜像文件
 # 本地 https 仓库
 ctr images pull -k 192.168.0.242:8000/tongrds-center:latest
 ctr images tag 192.168.0.242:8000/tongrds-center:latest tongrds-center
 ctr images delete 192.168.0.242:8000/tongrds-center:latest
```

 
```
 # ctr image import postgres.tar                     # import 本地镜像
 ctr images list                                     # 
```

 
```
 ctr run -d docker.io/library/nginx:alpine nginx     # run
 ctr container list
```


===crictl===
```
 # 单节点可见
 crictl pull bitnami/milvus
 crictl image
```


=== 状态检查 ===

==== 基础状态检查 ====
 ```

# 检查节点状态
kubectl get nodes -o wide

# 检查集群组件健康状态
kubectl get --raw='/readyz?verbose'

# 检查集群版本
kubectl version
```

==== 测试 ====
 ```

# 在任一节点上运行
kubectl run test-pod --image=${mirror}busybox --restart=Never -- sh -c "echo 'K8s is working.'"
kubectl logs test-pod        # K8s is working.
kubectl delete pod test-pod

# 通常在国内执行上面拉取，会遇到典型的 Docker Hub 访问限制问题。测试时可以简单地使用国内镜像仓库

# kubectl describe pod test-pod
mirror=swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/
```

==== Nginx ====

使用 Deployment 来部署 Nginx（指定 4 个副本数），并使用 NodePort Service 来暴露外部访问端口（映射到宿主机的端口，默认在 30000-32767 之间）。
 ```
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-test
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx-test
  template:
    metadata:
      labels:
        app: nginx-test
    spec:
      containers:
      - name: nginx
        image: swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/nginx:latest
        ports:
        - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx-test
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
EOF
```

检查分布情况
```
 kubectl get pods -o wide -l app=nginx-test
```

验证外部访问
```
 # 通过任何一个节点的 IP 端口来访问 Nginx
 <nowiki>http://192.168.0.xxx:30080
```

### Error

#### node NotReady
```
 kubectl apply -f calico.yaml
 kubectl apply -f kube-flannel.yml
```
```
 *<small>dorisfe1   NotReady   <none>                 8s     v1.28.2*
```

#### registry.mirrors
```
 即将删除的配置项
```
```
 *<small>WARN[0000] DEPRECATION: The `mirrors` property of `[plugins."io.containerd.grpc.v1.cri".registry]` is deprecated since containerd v1.5 and will be removed in containerd v2.0. Use `config_path` instead. *
```

#### kubectl get node
```
 节点需要 /etc/kubernetes/admin.conf
```
```
 *<small>E0325 16:13:46.489435   14081 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp [::1]:8080: connect: connection refused*
```

#### crictl info
```
 # /etc/crictl.yaml
 runtime-endpoint: unix:///run/containerd/containerd.sock
 image-endpoint: unix:///run/containerd/containerd.sock
 timeout: 2
 debug: false
 pull-image-on-create: false
 disable-pull-on-run: false
```
```
 *<small>E0326 10:15:02.860305    3920 remote_runtime.go:616] "Status from runtime service failed" err="rpc error: code = Unavailable desc = connection error: desc = \"transport: Error while dialing dial unix /var/run/dockershim.sock: connect: no such file or directory\""*
```
