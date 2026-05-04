---
source_title: Openssl
categories:
- Develop
- Linux
- Platform
last_modified: '2024-03-13T09:18:20Z'
---
The OpenSSL software - a robust, commercial-grade, full-featured toolkit for general-purpose cryptography and secure communication. 

__TOC__

ubuntu 20.04 自带 openssl，但无相应的 .h 及 .so .a
```
 *openssl
 OpenSSL> version
 OpenSSL 1.1.1f  31 Mar 2020*
```

可通过安装 libssl-dev 解决
```
 *apt install libssl-dev
 需要高版本的 openssl 报如下错误
 error while loading shared libraries: libcrypto.so.3*
```
```
 *# 删除，可选操作
 apt remove openssl libssl-dev
 # 以下目录及文件需手工删除
 /bin/openssl
 /bin/c_rehash
 /lib/ssl
 /usr/lib/ssl*
```

### 安装 openssl 3
```
 *apt install build-essential checkinstall zlib1g-dev -y
```

 
```
 wget https://www.openssl.org/source/openssl-3.0.8.tar.gz
```

 
```
 ./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl shared zlib
 make -j4
 make install_sw
```

 
```
 # /etc/ld.so.conf
 # add /usr/local/openssl/lib64
 # ldconfig
```

 
```
 # 配置路径
 export PATH=/usr/local/openssl/bin:$PATH
 export LD_LIBRARY_PATH=/usr/local/openssl/lib64:$LD_LIBRARY_PATH
```

 
```
 openssl version -a*
```

### zlib or zlib-dynamic
```
 *./config ... shared zlib
 ./config ... shared zlib-dynamic*
```

设置 openssl 编译时使用哪个方式(静态库、动态库)来获取 zlib 依赖。
