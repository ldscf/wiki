---
source_title: Docker 安装
categories:
- Develop
- Kubernetes
- Linux
last_modified: '2025-02-12T01:21:24Z'
---
Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版） 和 EE（Enterprise Edition: 企业版）。

#### docker-ce
- docker-ce 社区版
- docker-ee 商业版
```
 # Cenos 7
 wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
```
 
```
 yum install docker-ce docker-ce-cli -y
```
```
 # Ubuntu 20.04
 curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
 
```
 echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
 
```
 apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

##### start
```
 /etc/docker/daemon.json
 {
   "registry-mirrors": [
     "https://hub-mirror.c.163.com",
     "https://mirror.baidubce.com"
   ],
   "registry-mirrors":["http://192.168.0.242:5000"]
 }
```
 
```
 -.OR.-
 # systemctl daemon-reload
 /usr/lib/systemd/system/docker.service
 ExecStart=/usr/bin/dockerd --insecure-registry 192.168.0.242
```
 
```
 systemctl restart docker
```

##### 私有仓库

使用官方 registry 创建并运行一个私有仓库

###### http
```
 # docker pull docker.m.daocloud.io/library/registry
 # docker tag docker.m.daocloud.io/library/registry registry     # 复制一个新名称
 # docker run -d -p 8000:5000 --name="uh-registry" --restart=always -v ${TARGET}:${SOURCE} registry
 docker run -d -p 8000:5000 --name="uh-registry" --restart=always registry
 # docker exec -it --user root  /bin/sh
```

###### https
```
 # 修改 openssl.cnf，支持 IP 地址方式，HTTPS 访问
 #  ubuntu: /etc/ssl/openssl.cnf
 #  centos: /etc/pki/tls/openssl.cnf
 [ v3_ca ]
 subjectAltName= IP:192.168.0.242
```
 
```
 # 生成证书
 mkdir ~/certs
 openssl req -newkey rsa:2048 -nodes -keyout ~/certs/domain.key -x509 -days 365 -out ~/certs/domain.crt
```
 
```
 # COPY 证书到 docker 系统中
 # 使用 Docker Registry 需要将 .crt 拷贝到 /etc/docker/certs.d/[docker_registry_domain:端口 或者 IP:端口]/ca.crt
 cp domain.crt /etc/docker/certs.d/192.168.0.242:8000/ca.crt
```
 
```
 # .crt 放入系统的 CA 文件当中，使系统信任自签名证书
 # ubuntu
 cat domain.crt >> /etc/ssl/certs/ca-certificates.crt
 # centos
 cat domain.crt >> /etc/pki/tls/certs/ca-bundle.crt
```
 
```
 # 创建运行 docker 私有仓库容器，端口 8000，https 访问
 docker run -d -p 8000:8000 --name=uhry -v ~/certs/:~/certs -e REGISTRY_HTTP_ADDR=0.0.0.0:8000 -e REGISTRY_HTTP_TLS_CERTIFICATE=~/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=~/certs/domain.key registry
```
 
```
 # 验证
 https://192.168.0.242:8000/v2/_catalog
```
 

###### 上传
```
 # 若使用 http，则需要配置 /etc/docker/daemon.json 并重启
 "insecure-registries": [ "192.168.0.242:8000" ]
```
 
```
 # An image locally with the tag: 192.168.0.242:8000/
 docker tag redis 192.168.0.242:8000/redis
 docker push 192.168.0.242:8000/redis
 # http://192.168.0.242:8000/v2/_catalog
 #  {"repositories":["redis"]}
```
 
```
 # login, 如果没有密码，随便输入
 docker login 192.168.0.242:8000
 # Your password will be stored unencrypted in /root/.docker/config.json
```

###### 下载
```
 # 查看镜像: /v2/_catalog
 # 查看 TAG: /v2/library/tongrds-center-test/tags/list
 # 用户名密码: curl -u user:passwd 
 # 先 docker login, 登录认证 /.docker/config.json
 # 除非 TAG 有 latest 版本，否则均需加 TAG。如：2.2.C.1-3
 docker pull 192.168.0.89:80/library/tongrds-center-test:2.2.C.1-3
```

##### 容器操作
```
 # 停止
 docker stop 
 # 删除
 docker rm 
 # 执行 ps -a 中存在
 docker start 
```
 
```
 # 正在运行的容器
 docker ps
```
 
```
 # 所有定义的容器
 docker ps -a
```
 
```
 # 删除镜像
 docker rmi 
 # 以区分开为准，如：docker rmi redis:v2
 REPOSITORY  TAG
 redis       latest
 redis       v2
```
 
```
 # 标记本地镜像，如果 tag 有版本，需要指定，如：
 docker tag postgres:12 192.168.0.242:8000/postgres
 ---
 REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
 redis        latest    1a83fd5edeed   2 weeks ago    117MB
 postgres     12        feaa4007f007   4 weeks ago    419MB
```

#### daemon.json

Docker Engine 的配置管理文件, 里面几乎涵盖了所有 docker 命令行启动可以配置的参数

[daemon.json配置文件详解](https://www.cnblogs.com/chuyiwang/p/17577020.html)
```
 /etc/docker/daemon.json
```
 
```
 registry-mirrors
 * https://.mirror.aliyuncs.com  # 阿里云镜像站(需登录)
 * http://hub-mirror.c.163.com              # 网易云镜像站
 * https://mirror.baidubce.com              # 百度云镜像站
 * https://docker.mirrors.sjtug.sjtu.edu.cn # 上海交大镜像站
 * https://docker.nju.edu.cn                # 南京大学镜像站
 * https://registry.docker-cn.com           # Docker 中国官方镜像(已关闭)
 * https://docker.mirrors.ustc.edu.cn       # 中国科技大学 USTC(仅供内部访问)
```
 
```
 log-driver = [json-file]
 log-level = debug, [info], warn, error, fatal
```
```
 cat > /etc/docker/daemon.json << EOF
 {
     "registry-mirrors": [
         "https://mirror.baidubce.com",
         "http://hub-mirror.c.163.com",
         "https://docker.mirrors.sjtug.sjtu.edu.cn"
     ],
     "exec-opts": ["native.cgroupdriver=systemd"],
     "max-concurrent-downloads": 10,
     "max-concurrent-uploads": 5,
     "log-level": "info",
     "log-opts": {
         "max-size": "100m",
         "max-file": "2"
     },
     "live-restore": true
 }
 EOF
```

#### 启动docker服务
```
 # systemctl daemon-reload
 # systemctl enable docker
 systemctl start docker
```

#### docker 版本

上面安装的是最新版本，也可以指定版本安装
```
 # List docker
 yum list docker-ce.x86_64 --showduplicates
 --> docker-ce.x86_64            3:19.03.9-3.el7                    docker-ce-stable
 yum install docker-ce-19.03.9-3.el7 docker-ce-cli-19.03.9-3.el7
```

#### docker CMD
```
 docker info
 docker run hello-world      # 创建一个新的容器
 docker run -itd ubuntu:20.04 /bin/bash
 <-i: 交互式操作
   -t: 终端
   -d: 指定容器的运行模式为后台
   ubuntu: ubuntu 镜像
   /bin/bash: 命令，指定使用 /bin/bash 作为交互式 Shell
 # 以上是创建一个新的容器，如果已经创建，只是停止(在 ps -a 中可见)，启动命令是：docker start CONTAINER_ID
```
 
```
 docker ps               # 查看启动的容器（ps -a 所有已创建的容器）
 docker exec             # 进入容器
 docker start            # 启动容器
 docker stop             # 停止容器
 docker rm               # 删除容器
 docker export           # 导出容器快照
 docker import           # 导入容器快照
 docker images           # 列出本地镜像
 docker search           # 从 Docker Hub 搜索镜像
 docker pull             # 下载镜像
 docker rmi              # 删除镜像
 docker save             # 导出镜像
 docker load             # 导入镜像
```
```
 *<<b>docker ps -a</b>
 CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                     PORTS     NAMES
 c9aebfaf8fd5   ubuntu:20.04   "/bin/bash"   53 seconds ago   Up 52 seconds                         jovial_jepsen
 bf32c8a5c049   hello-world   "/hello"   9 minutes ago    Exited (0) 9 minutes ago             great_carver
 1538a050ab4c   hello-world   "/hello"   10 minutes ago   Exited (0) 7 seconds ago             inspiring_booth
```

 
```
 docker export c9aebfaf8fd5 > ubuntu2204.tar
 docker import ubuntu2204.tar test/ubuntu:v1
```

 
```
 docker exec -it c9aebfaf8fd5 /bin/bash
 exit
```

 
```
 <b>docker images</b>
 REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
 python        3.9.19    14dfba14e806   2 days ago      997MB
 postgres      latest    b9390dd1ea18   4 weeks ago     431MB
 ubuntu        20.04     3cff1c6ff37e   4 weeks ago     72.8MB
 hello-world   latest    d2c94e258dcb   10 months ago   13.3kB
```

 
```
 # export & save
 docker export <CONTAINER ID> -o ubuntu.tar
 docker import - <REPOSITORY_NAME> < ubuntu.tar
```

 
```
 docker save -o ubuntu2004.tar ubuntu
 docker load --input ubuntu2004.tar*
```

#### [Docker Hub](https://hub.docker.com)
 ```

# docker search postgres
NAME                       DESCRIPTION                                     STARS     OFFICIAL
postgres                   The PostgreSQL object-relational database sy…   13884     [OK]
circleci/postgres          The PostgreSQL object-relational database sy…   32
ubuntu/postgres            PostgreSQL is an open source object-relation…   40

# docker pull ubuntu/postgres

# docker images
REPOSITORY        TAG          IMAGE ID       CREATED        SIZE
ubuntu/postgres   latest       c4011a015c92   3 months ago   408MB
```

#### 私有仓库 registry

##### 上传镜像
 ```

## download from remote
docker pull 192.168.0.242:8000/redis:latest
docker tag 192.168.0.242:8000/redis:latest redis:1.0

# docker rmi 192.168.0.242:8000/redis

# save to file
docker save -o redis-v1.0.tar redis:1.0
gzip redis-v1.0.tar

## load from local
docker import redis-v1.0.tar.gz redis:1.0
docker tag redis:1.0 192.168.0.242:8000/redis:1.0
docker push 192.168.0.242:8000/redis:1.0

# 查看镜像列表
https://192.168.0.22:8000/v2/_catalog
```

##### 删除镜像
 ```

# CONTAINER ID
docker ps|grep registry
docker exec -it 322f00c39f82 sh
rm -rf  /var/lib/registry/docker/registry/v2/repositories/<镜像名称>
/bin/registry garbage-collect /etc/docker/registry/config.yml
docker restart 322f00c39f82
```

#### Error
- Error response from daemon: Get "https://192.168.0.89:80/v2/": http: server gave HTTP response to HTTPS client

需要在客户端 docker 配置 /etc/docker/daemon.json，并重启。
```
 "insecure-registries": [ "192.168.0.89:80" ]
 docker login 192.168.0.89:80
```
- Error response from daemon: Get "https://192.168.0.22:8000/v2/": tls: failed to verify certificate: x509: certificate signed by unknown authority

与 上面情况类似，需要在客户端 docker 配置 /etc/docker/daemon.json，并重启。
```
 "insecure-registries": [ "192.168.0.22:8000" ]
```
