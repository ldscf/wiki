---
source_title: Python
categories:
- Develop
- Python
last_modified: '2024-07-29T07:06:11Z'
---
Python 是一种解释型、面向对象、动态数据类型的高级程序设计语言。

Python 由 Guido van Rossum 于 1989 年底发明，第一个公开发行版发行于 1991 年。

像 Perl 语言一样, Python 源代码同样遵循 GPL(GNU General Public License) 协议。

官方宣布，2020 年 1 月 1 日， 停止 Python 2 的更新。

Python 2.7 被确定为最后一个 Python 2.x 版本。

### 环境

Python 从 3.11 之后，采用了 SSL 的加密方式，需要依赖 openssl-1.1.1。--enable-optimization 优化选项，需要依赖 gcc 9 以上版本。

Python 3.12 不再支持 readline 模块，因此无法直接使用上光标键查看命令行历史。

#### 安装依赖包
```
 # yum update -y
 yum install -y git make cmake htop gcc gcc-c++ kernel-devel bzip2 bzip2-devel mlocate sqlite-devel zlib zlib-devel libffi-devel openssl-devel libcurl-devel chrony  wget dmidecode net-tools openssh-server openssh-client perl-CPAN perl-IPC-Cmd
 - OR - 
 yum -y groupinstall "Development tools"
 yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
 yum install libffi-devel -y
```

#### openssl
```
 curl https://www.openssl.org/source/openssl-3.0.12.tar.gz -O openssl-3.0.12.tar.gz
 ./config --prefix=/opt/openssl
 make -j4 && make install
 ln -sf /opt/openssl/bin/openssl /usr/bin/openssl
 # ln -s /opt/openssl/lib64 /opt/openssl/lib
 echo "/opt/openssl/lib/" >> /etc/ld.so.conf
 ldconfig -v
 openssl version
```

#### python
```
 curl https://www.python.org/ftp/python/3.11.7/Python-3.11.7.tgz -O Python-3.11.7.tgz
 ./configure --prefix=/opt/python3.11 --with-openssl=/opt/openssl --with-openssl-rpath=auto
 make -j4 && make install
 # /usr/local/bin/python3
 # import ssl
```

#### upgrade
```
 python3 -m pip install --upgrade pip
 python3 -m pip install --upgrade numpy
 # tensorflow-cpu-aws 2.12.0 requires numpy<1.24,>=1.22, but you have numpy 1.24.4 which is incompatible.
 pip install numpy==1.23.5
```
