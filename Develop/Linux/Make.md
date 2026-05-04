---
source_title: Make
categories:
- Develop
- Linux
last_modified: '2024-12-31T05:12:41Z'
---
Linux C/C++ 源码安装一般包括几个步骤：配置（configure），编译（make），安装（make install）。

当使用 autoconf/automake 来构建软件包时，会提供一个带有几个标准参数和（有时）额外的自定义参数的 configure 脚本。有些软件包不使用 autoconf，但通常提供一个兼容的 configure 脚本。

一般情况下，默认即可：
```
 ./configure
 make
 make install
```

### configure

configure 是一个可执行脚本，在源码目录中执行可以完成自动的配置工作(生成 Makefile，以及 config.status、config.log)。

默认情况下：
- /usr/local
  - bin: 可执行文件
  - lib: 库文件
  - etc: 配置文件
  - share: 资源文件
```
 # configure --prefix=/opt/ABC
 # 改变安装路径，方便后续的卸载，rm -rf /opt/ABC 即可删除 ABC。
 # 注意，上面的路径可能只是个相对路径，如果在后面用 DESTDIR 指定路径，那么会安装在 DESTDIR/opt/ABC 目录。
```

### make
```
 # 4 个线程并行
 make -j4
```

### make install
```
 make prefix=/opt/ABC DESTDIR=/usr/local install
 # DESTDIR，指定 prefix 目录前的绝对路径
```
