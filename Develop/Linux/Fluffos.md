---
source_title: Fluffos
categories:
- Develop
- Linux
- Platform
last_modified: '2023-04-02T11:13:46Z'
---
FluffOS is an LPMUD driver, based on the last release of MudOS (v22.2b14), includes 10+ years of bug fixes and performance enhancement, with active support. FluffOS supports all LPC based mud with very little code changes.

FluffOS 目前有 v2017 和 v2019 二个版本
- v2017 版可以兼容支持旧MUD，只需对LIB微调即可
- v2019 版要求LIB编码为 utf-8 ，支持websocket

v2017 版目前支持在以下系统中编译：ubuntu 14.04+, raspbian, centos 7+, CYGWIN 64。

v2019 版目前支持在以下系统中编译：ubuntu 18.04+ (包括 WSL), raspbian, OSX, MSYS2/mingw64.

### ENV
- ubuntu 20.04.01 LTS
- RAM 1G, AMD EPYC 7551 32-Core Processor *2, 1996.251 MHz

### INIT
```
 apt update
 apt install build-essential bison libevent-dev libmysqlclient-dev libpcre3-dev libpq-dev libsqlite3-dev libssl-dev libz-dev libjemalloc-dev libicu-dev
 apt install libdw-dev libbfd-dev
```

### fluffos v2019
```
 git clone https://github.com/fluffos/fluffos.git
 cd fluffos
```
 
```
 # git checkout master
 mkdir build
 cd build
```
 
```
 # apt install cmake
 cmake ..
 make install -j4
```

### MUD xkx100
```
 git clone --recurse-submodules https://github.com/MudRen/xkx100.git
 # cp ../fluffos/build/bin/driver .
```
 
```
 ./driver config.ini
```

### MUD下载
- [MUDLIB fluffos v2017版｜v2019版及其它经典版下载汇总](https://mud.ren/threads/146) 
- [中文MUD大全](http://mudchina.github.io/)
