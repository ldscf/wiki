---
source_title: PyCharm
categories:
- Develop
- Python
last_modified: '2025-09-07T09:09:58Z'
---
PyCharm 是由 JetBrains 开发的 Python IDE（Integrated Development Environment，集成开发环境），带有一整套可以帮助用户在使用 Python 语言开发时提高其效率的工具，比如调试、语法高亮、项目管理、代码跳转、智能提示、自动完成、单元测试、版本控制。此外，该 IDE 提供了一些高级功能，以用于支持 Django 框架下的专业 Web 开发。

### 安装

#### [Python](https://www.python.org/downloads/)
```
 [python-3.10.8-amd64](https://www.python.org/ftp/python/3.10.8/python-3.10.8-amd64.exe)
 # 版本: 3.10.8，28 M
 # Path: C:\Users\ldscf\AppData\Local\Programs\Python\Python310
```

#### [PyCharm](https://www.jetbrains.com.cn/edu-products/download/?section=pycharm-edu)
```
 # 版本: 2025.1.3，633 M
 # 默认安装目录：C:\Program Files\JetBrains\PyCharm Community Edition 2025.1.3
 # 因为含有空格，运行时会报错。
 # 安装到一个没有空格的目录，或者将上面已经安装完毕的目录拷出来
```

##### AI
```
 文件 -> 设置 -> 插件
 通义灵码
 安装后在右侧边条出现
 P.S. 搜索时，还会出现一个所谓的"GPT4_III"，是一个“有骨气”的国人开发的，只是名称叫 “GPT4”。
```

#### MySQL
```
 [MySQL Community Server 8.4.5 LTS](https://dev.mysql.com/downloads/mysql/)
 # 版本: 8.4.5 LTS，129 M
```
```
 在 MySQL 菜单中运行：MySQL 8.4 Command Line Client
```

### Setup
```
 python.exe -m pip install --upgrade pip
 pip install -r requirements.txt
```
```
 PyCharm 中，File -> Settings -> 选择项目 -> Python 解释器 -> 添加本地解释器 -> 选择现有
```

特别说明：每个存放 py 的子目录，一定要有 __init__.py，否则 pycharm 不认目录（import a.b.c，如果目录 b 中无 __init__.py，则报错）。

### Project

新建或打开一个已有目录均可（可能会自动建立 start.py 文件）

#### Run

在 PyCharm 中运行，会比直接在目录下 python start.py 有更严格的检查。

### Error

#### 目录含空格

确保 PyCharm、Python 等安装目录及项目所在目录均不包含空格，否则出现下列警告：
```
 C:\Users\ldscf\AppData\Local\Programs\Python\Python310\python.exe: can't open file 'C:\\Program': [Errno 2] No such file or directory
```

#### PIN
 ```
正常运行后，会出现下面信息，有用的信息包括 Web 地址:端口，以及 Debugger PIN。
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://192.168.0.122:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 590-159-650
```
