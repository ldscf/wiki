---
source_title: K8s
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2024-07-04T01:52:28Z'
---
2010年，dotCloud 公司在美国旧金山成立，后来改名为 Docker。主要提供基于 PaaS 的云计算技术服务。具体来说，是和 LXC(Linux Containers) 有关的容器技术，产品命名为 Docker，并于 2013 年开源。

Google 在 Docker 兴起的同时也把内部生产验证的容器 lmctfy（Let Me Container That For You, [lem-kut-fee]）开源，但无法应对 Docker 的崛起。后来 Google、RedHat 等共同牵头发起了一个名为 CNCF（Cloud Native Computing Foundation）的基金会。把 Google 最底层也是最核心的 Borg 拿出来开源，并托管给 CNCF 基金会，即是 Kubernetes。

Kubernetes 社区在 2016 年之后得到了空前的发展，2017 年 Docker 公司先是将 Docker 项目的容器运行时部分 Containerd 捐赠给 CNCF 社区，然后 Docker 公司宣布将 Docker 项目改名为 Moby，然后交给社区自行维护，放弃在开源社区同 Kubernetes 生态的竞争，而 Docker 公司的商业产品将保留 Docker 注册商标。

2017 年 10 月，Docker 公司宣布将在 Docker 企业版中内置 Kubernetes 项目，这标志着持续了近两年之久的“编排之争”至此落下帷幕。

### Containerd

Containerd 是一个用于管理容器的开源项目，它起源于 Docker 项目。

Docker 引擎与 Containerd 之间的关系可以被视为 “上游” 和 “下游” 的关系。Docker 引擎构建在 Containerd 之上，它使用 Containerd 来管理容器的基本功能。因此，Containerd 可以被认为是 Docker 引擎的核心部分之一。

Containerd 专注于容器的基本操作，如镜像管理、容器运行时和容器生命周期管理等，它提供了一个统一的接口，供容器管理工具使用。

### Docker

Docker 是创建容器的工具，Docker 镜像，是一个特殊的文件系统，类似于沙箱方式提供容器运行时所需的程序、库、资源、环境变量等。用镜像生成的一个环境，就是容器。

Docker Swarm 是 Docker 公司推出的容器集群管理方案，应用不如 K8s 广泛。

### kubernetes

Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications.

It groups containers that make up an application into logical units for easy management and discovery. Kubernetes builds upon 15 years of experience of running production workloads at Google, combined with best-of-breed ideas and practices from the community.

K8s 是拥有容器编排能力(自动化管理容器化应用程序的部署、扩展、运行和维护)的集群管理解决方案。支持对接多种容器，如 Docker, CoreOS Rkt(Rocket)，Apache Mesos 等。

从 K8s v1.24 开始，删除了对 Docker（Dockershim）的容器运行时接口（CRI）的支持(不再原生支持 Docker)。同时Amazon EKS 官方也将 Containerd 作为唯一的运行时。

K8s(Kubernetes)包括两部分：
- 一个 Master 节点（主节点）
- 一组 Node 节点（集群计算节点）

#### Master
- API Server: 系统对外接口，供客户端和其它组件调用
- Scheduler: 资源调度
- Controller manager: 管理控制器
- etcd: 

#### Node
- Pod: 基本操作单元。一个Pod代表着集群中运行的一个进程，它内部封装了一个或多个紧密相关的容器
- Service: 一组提供相同服务的Pod的对外访问接口
- Docker: 创建容器
- kubelet: 监视指派到它所在Node上的Pod，包括创建、修改、监控、删除等
- kube-proxy: 为Pod对象提供代理
- Fluentd: 日志收集、存储与查询
- kube-dns(可选)
