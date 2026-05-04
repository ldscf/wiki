---
source_title: K8s Error
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2025-07-08T06:47:26Z'
---

### kubeadm join
- Warning  InvalidDiskCapacity  kubelet  invalid capacity 0 on image filesystem 
```
 原因：问题出在以前加入过集群，现在是换个新集群加入。上面的信息意味着：kubelet 无法检测或访问容器运行时的镜像存储目录（/var/lib/containerd），导致它认为磁盘容量为 0，从而使节点进入 NotReady 状态。
 systemctl stop containerd
 rm -rf /var/lib/containerd
 systemctl start containerd
```
 
```
 kubectl get nodes
 STATUS: NotReady --> Ready
```
