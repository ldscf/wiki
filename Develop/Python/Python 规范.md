---
source_title: Python 规范
categories:
- Develop
- Python
last_modified: '2025-08-05T03:14:18Z'
---
Python Style

Python 开发规范

### 命名Style
- 模块级变量命名: 文件开头，前后双下划线，__version__ = '1.0'
- 类命命名: 驼峰，UsrClass
- 变量命名: 小写字母+下划线，res_line
- 常量命名: 大写字母+下划线，MAX_RENT_LENGTH = 1024
- 模块变量或函数内部使用命名: 前下划线，_res

避免：
- 保留字
- 单字符名称
- 使用连字符(-)

### 注释Style

#### 文档注释
```
　　Python使用文档字符串作为注释方式: 文档字符串是包, 模块, 类或函数里的第一个语句. 这些字符串可以通过对象的doc成员被自动提取, 并且被pydoc所用. 我们对文档字符串的惯例是使用三重双引号”“”( PEP-257 )。
```
```
　　一个文档字符串应该这样组织:
```
- 首先是一行以句号, 问号或惊叹号结尾的概述(或者该文档字符串单纯只有一行). 接着是一个空行.
- 接着是文档字符串剩下的部分, 它应该与文档字符串的第一行的第一个引号对齐.
```
 """A user-created :class:`Response ` object.
```
     
```
 Used to xxx a :class: `JsonResponse `, which is xxx
```
 
```
 :param data: response data
 :param file: response files
```
     
```
 Usage::
```
 
```
     >>> import api
     >>> rep = api.Response(url="https://www.google.com")
 """
```

#### 行内注释
```
　　行内注释是与代码语句同行的注释(PEP8)。
```
- 行内注释和代码至少要有两个空格分隔
- 注释由#和一个空格开始
```
 x = x + 1                 # Compensate for border
```

#### 模块
```
　　每个文件应该包含一个许可样板. 根据项目使用的许可(例如, Apache 2.0, BSD, LGPL, GPL), 选择合适的样板.
 # -*- coding: utf-8 -*-
 # (C) JiaaoCap, Inc. 2017-2018
 # All rights reserved
 # Licensed under Simplified BSD License (see LICENSE)
```

#### TODO 注释
- 开头包含「TODO」字符串
- 紧跟着是用括号括起来的名字或者 email
- 再接下来是冒号，然后写接下来要做内容的文字解释
```
　　如：# TODO(email/name): 内容
```

### 开发style

#### 模块引用
```
　　通用功能模块部分放在文件的开头，并按照下列顺序引用：
```
- 标准库 imports
- 相关第三方 imports
- 本地应用/库的特定 imports
```
　　非通用部分在子模块或使用功能时引用
```

避免：
- import *

#### 二元运算符换行
```
 res = (  a1
        + a2
        - b1
        - c1)
```

#### 字符串
```
　　使用~~单引号~~双引号来表示字符串。因为 SQL 字符串使用的是单引号。
```

#### 函数
- 函数参数、返回值为字典
  - 参数提供默认值
  - 返回值中：
  - *_result=0为正常，=1为失败，其他为可定义异常
  - *_rs 为操作数据行数
  - *_message为参考信息
  - *_memo 为操作日志
- 循环提供额外退出条件
  - 如到达一定次数、时间等
- 操作日志
  - DB日志：log.l_serv_monitor
  - 文件日志：当前目录或~/log/
