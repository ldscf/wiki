---
source_title: Python 源码保护
categories:
- Develop
- Python
last_modified: '2026-10-08T02:58:23Z'
---
### 概述

本文介绍如何使用 Cython 将 Python 项目中的关键模块编译为原生扩展模块（.so），再结合 PyInstaller 将项目打包为单文件可执行程序的完整流程。

该方案主要适用于以下场景：
- 提高核心 Python 模块的运行性能。
- 提高核心 Python 源码的逆向门槛。
- 降低生产环境对 Python 源码和依赖安装的要求。
- 将应用及其运行时依赖封装为独立的可执行文件。

需要注意的是，Cython 编译主要用于提高源码保护程度和逆向成本，不能视为绝对意义上的源码加密或不可逆保护。

### 整体流程

整个发布流程分为以下几个阶段：
1. 创建独立的编译目录，根据同步清单复制必要文件。
1. 使用 Cython 将指定的核心 Python 模块编译为 .so 扩展模块。
1. 精确清理已经编译的 .py 源码及中间文件，同时保留必要的包结构文件。
1. 使用 PyInstaller 进行单文件打包，并根据实际依赖处理动态导入、数据文件和包资源。
1. 整理发布目录，将需要独立维护的配置文件和资源文件放到可执行文件外部。
1. 在与开发环境隔离的环境中执行发布包验证。

整体流程如下：

Python 源码

    |

    +-- 核心模块

    |      |

    |      +-- Cython

    |             |

    |             +-- .so

    |

    +-- 其他 Python 模块

           |

           +-- PyInstaller

                  |

                  +-- 单文件可执行程序

                         |

                         +-- 外部配置

                         |

                         +-- 外部可维护资源

### 准备工作

项目中应准备以下内容：
- Cython 编译脚本，例如 build_libs.py。
- 文件同步清单，例如 sync_file_list.txt。
- PyInstaller 的入口程序，例如 main.py。
- 项目运行所需要的静态资源和依赖。

同步清单必须包含 Cython 编译所需的源码、包结构文件以及编译脚本本身。

例如：

main.py

build_libs.py

sync_file_list.txt

core/__init__.py

core/engine.py

core/llm.py

core/security.py

...

其中，__init__.py 应明确保留。

对于本发布流程，__init__.py 是包结构的一部分。虽然某些 Python 场景支持 namespace package，但不应依赖这种行为来完成发布打包。

建议在源码目录的上级目录执行整个流程，从而将编译环境与原始源码目录隔离。

### 步骤一：创建独立编译环境

首先创建一个独立的编译目录：
- 生成时间戳。
- 如果旧编译目录存在，则先移动到临时备份目录。
- 创建新的编译目录。
- 根据同步清单精确复制所需文件。

示例：
1. !/usr/bin/env bash

set -euo pipefail

TIMEID=$(date '+%Y%m%d%H%M%S')

SOURCE="your_project"

COMPILE="your_project_release"

if [ -d "${COMPILE}" ]; then

    mv "${COMPILE}" "/tmp/${COMPILE}_${TIMEID}"

fi

mkdir -p "${COMPILE}"

rsync -av \

    --relative \

    --files-from="${SOURCE}/sync_file_list.txt" \

    "${SOURCE}/" \

    "${COMPILE}/"

cd "${COMPILE}"

这里使用独立编译目录的主要目的，是避免后续删除 .py 文件时影响原始源码。

### 步骤二：执行 Cython 编译

进入编译目录后，执行 Cython 编译脚本，将需要保护或优化的核心模块编译为原生扩展模块。

例如：

python build_libs.py build_ext --inplace

成功后，通常会产生类似以下文件：

core/

├── __init__.py

├── engine.cpython-312-x86_64-linux-gnu.so

├── llm.cpython-312-x86_64-linux-gnu.so

└── security.cpython-312-x86_64-linux-gnu.so

实际文件名会根据 Python 版本、操作系统和 CPU 架构不同而变化。

例如：

engine.cpython-312-x86_64-linux-gnu.so

而不是固定的：

engine.so

因此，后续清理时不应依赖固定的 .so 文件名。

### 步骤三：精确清理源码

Cython 编译成功并确认扩展模块已经生成后，再删除对应的 Python 源码。

例如：

rm -f core/llm.py

rm -f core/security.py

rm -f core/engine.py

rm -f core/*.c

rm -rf build/

find . -type d -name "__pycache__" -prune -exec rm -rf {} +

需要特别注意：

**不要删除 __init__.py。**

例如：

core/

├── __init__.py

├── engine.cpython-312-....so

├── llm.cpython-312-....so

└── security.cpython-312-....so

如果项目存在多级 Python 包，则应分别保留各级包所需的 __init__.py。

#### 清理原则

清理操作应满足以下要求：

核心模块：

    xxx.py        → 删除

    xxx.c         → 删除

    xxx*.so       → 保留

Python 包：

    __init__.py   → 保留

其他运行所需 Python 源码：

    *.py          → 根据项目实际情况保留

不要使用过于宽泛的删除命令，例如：

find . -name "*.py" -delete

否则可能误删：
- 应用入口。
- PyInstaller hook。
- 运行时动态加载模块。
- 配置加载代码。
- 包初始化代码。
- 其他尚未进行 Cython 编译的 Python 模块。

### 步骤四：使用 PyInstaller 打包

完成 Cython 编译和源码清理后，再使用 PyInstaller 进行打包。

基本形式：

pyinstaller \

    --onefile \

    --noconfirm \

    --name your_app_name \

    main.py

实际项目通常还需要处理动态导入和静态资源。

#### --add-data

用于将应用运行所需要的数据文件或目录加入打包结果。

例如：

--add-data "index.html:." \

--add-data "css:css"

Linux/macOS 使用冒号分隔源路径和目标路径：

源路径:目标路径

Windows 使用分号：

源路径;目标路径

因此，如果发布脚本需要跨平台使用，应注意分隔符差异。

#### --hidden-import

当 PyInstaller 无法通过静态分析发现动态导入的模块时，需要显式指定：

--hidden-import "core.engine"

--hidden-import "core.llm"

--hidden-import "core.security"

是否需要 hidden-import 应以实际打包分析和运行验证为依据，而不是机械地为所有模块添加。

#### --collect-submodules

用于收集一个包下面的多个子模块，例如：

--collect-submodules fastapi

--collect-submodules uvicorn

#### --collect-all

用于一个依赖需要同时收集以下内容的情况：
- Python 模块。
- 数据文件。
- 二进制扩展。
- 其他包资源。

例如：

--collect-all chromadb

不建议无条件使用 --collect-all。它可能显著增加最终程序体积，应根据实际运行需要确定。

#### --copy-metadata

部分第三方库会在运行时通过 importlib.metadata 等机制查询包元数据。

这种情况下可以添加：

--copy-metadata "tqdm"

--copy-metadata "regex"

--copy-metadata "requests"

--copy-metadata "packaging"

同样应根据实际依赖确定，而不是默认全部添加。

#### PyInstaller 示例

例如：

pyinstaller \

    --onefile \

    --noconfirm \

    --name your_app_name \

    --add-data "index.html:." \

    --add-data "css:css" \

    --add-data "chroma_db:chroma_db" \

    --hidden-import "core.engine" \

    --hidden-import "core.llm" \

    --hidden-import "core.security" \

    --hidden-import "openai" \

    --hidden-import "anyio._backends._asyncio" \

    --collect-submodules fastapi \

    --collect-submodules uvicorn \

    --collect-all chromadb \

    --copy-metadata "tqdm" \

    --copy-metadata "regex" \

    --copy-metadata "requests" \

    --copy-metadata "packaging" \

    main.py

上述参数仅为示例，最终参数应以项目实际依赖和 PyInstaller 的分析结果为准。

特别是 sqlite3，它属于 Python 标准库，不建议默认写成：

--collect-all sqlite3

只有在实际打包或运行验证表明需要额外处理时，才应针对具体问题增加配置。

### 步骤五：处理外部配置和资源

--onefile 会将应用及其打包资源封装到单个可执行文件中。

但对于需要在运营期间修改的配置、词库或数据库等资源，不建议全部打进单文件。

例如：

dist/

├── your_app_name

└── config/

    └── sensitive.txt

可以执行：

mkdir -p dist/config

cp "../${SOURCE}/config/sensitive.txt" \

   "dist/config/sensitive.txt"

应用程序运行时从外部目录读取该文件。

这样修改配置时无需重新执行 Cython 编译和 PyInstaller 打包。

#### 数据库和大型资源

对于类似 chroma_db/ 这样的数据库或大型数据目录，应根据实际使用方式决定是否打入 PyInstaller。

如果数据需要在部署后继续更新，通常不建议：

--add-data "chroma_db:chroma_db"

而更适合将其作为外部运行时资源：

dist/

├── your_app_name

├── config/

│   └── ...

└── chroma_db/

    └── ...

这样可以避免：
- 单文件体积过大。
- 每次启动都需要从 onefile 临时目录释放大量数据。
- 运行期间对内置数据库进行修改。
- 更新数据库后必须重新打包应用。

如果数据库本身是只读、体积较小且希望随应用一起发布，则可以考虑使用 --add-data。

### 步骤六：验证发布包

**PyInstaller 输出成功并不代表发布包一定可以运行。**

建议至少进行以下验证。

#### 文件存在性

确认以下文件存在：

dist/your_app_name

并且具有执行权限。

#### 独立环境启动

不要直接在开发源码目录中测试。

应将 dist/ 复制到独立目录或干净环境中运行。

#### 核心功能验证

至少验证：
- 应用能够正常启动。
- Cython 模块能够正常加载。
- 动态导入模块能够正常加载。
- 静态资源能够正常读取。
- 外部配置能够正常读取。
- 数据库能够正常访问。
- 网络/API 功能正常。
- 退出流程正常。

#### 源码依赖验证

发布环境中不应再依赖原始项目目录中的核心模块源码。

否则说明打包配置仍然存在隐式源码依赖。

### 完整示例脚本

以下脚本作为参考模板。实际项目应根据源码结构、Cython 模块以及 PyInstaller 依赖进行调整。
1. !/usr/bin/env bash

set -euo pipefail
1. 在源码目录下运行

cd ..

TIMEID=$(date '+%Y%m%d%H%M%S')

SOURCE="your_project"

COMPILE="your_project_release"

echo "========== 1. 创建编译环境 =========="

if [ -d "${COMPILE}" ]; then

    mv "${COMPILE}" "/tmp/${COMPILE}_${TIMEID}"

fi

mkdir -p "${COMPILE}"

echo "========== 2. 同步必要文件 =========="

rsync -av \

    --relative \

    --files-from="${SOURCE}/sync_file_list.txt" \

    "${SOURCE}/" \

    "${COMPILE}/"

cd "${COMPILE}"

echo "========== 3. Cython 编译 =========="

python build_libs.py build_ext --inplace

echo "========== 4. 清理已编译源码 =========="

rm -f core/llm.py

rm -f core/security.py

rm -f core/engine.py

rm -f core/*.c

rm -rf build/

find . \

    -type d \

    -name "__pycache__" \

    -prune \

    -exec rm -rf {} +

echo "========== 5. PyInstaller 打包 =========="

pyinstaller \

    --onefile \

    --noconfirm \

    --name "your_app_name" \

    --add-data "index.html:." \

    --add-data "css:css" \

    --add-data "chroma_db:chroma_db" \

    --hidden-import "core.engine" \

    --hidden-import "core.llm" \

    --hidden-import "core.security" \

    --hidden-import "openai" \

    --hidden-import "anyio._backends._asyncio" \

    --collect-submodules fastapi \

    --collect-submodules uvicorn \

    --collect-all chromadb \

    --copy-metadata "tqdm" \

    --copy-metadata "regex" \

    --copy-metadata "requests" \

    --copy-metadata "packaging" \

    main.py

echo "========== 6. 整理外部配置 =========="

mkdir -p dist/config

cp "../${SOURCE}/config/sensitive.txt" \

   "dist/config/sensitive.txt"

echo "========== 7. 发布包检查 =========="

if [ ! -f "dist/your_app_name" ]; then

    echo "ERROR: PyInstaller 输出文件不存在"

    exit 1

fi

echo

echo "打包成功！"

echo "可执行文件: ${COMPILE}/dist/your_app_name"

echo "外部配置:   ${COMPILE}/dist/config/sensitive.txt"

### 使用方法

将上述脚本保存为 compile.sh，然后执行：

chmod +x compile.sh

进入源码目录后执行：

./compile.sh

打包完成后，发布目录例如：

your_project_release/

└── dist/

    ├── your_app_name

    └── config/

        └── sensitive.txt

将 dist 目录作为发布包即可。

### 注意事项

#### Cython 并不等于绝对源码保护

Cython 会将 Python 模块转换为原生扩展模块，从而提高源码获取和逆向分析的成本，但 .so 仍然可以被分析。

因此，更准确的说法是：

> 

Cython 用于提高核心源码的保护程度和逆向门槛，而不是提供密码学意义上的源码加密。

#### 必须保证同步清单完整

sync_file_list.txt 必须包含：
- 应用入口。
- Cython 编译脚本。
- Cython 所需源码。
- 所有必要的 __init__.py。
- PyInstaller 所需的 hook、配置和资源。
- 应用运行所需的其他文件。

#### 不要删除 __init__.py

对于本发布流程，__init__.py 应视为 Python 包结构的一部分并明确保留。

#### 不要盲目增加 PyInstaller 参数

--hidden-import、--collect-submodules、--collect-all 和 --copy-metadata 都应该针对实际问题添加。

参数过多会导致：
- 发布文件变大。
- 打包时间增加。
- 启动解包时间增加。
- 依赖关系变得不透明。
- 后续维护困难。

#### 外部资源与内置资源应明确区分

建议遵循以下原则：

程序自身依赖

    → PyInstaller

部署后需要修改的配置

    → 外置

运行期间需要持续更新的数据

    → 外置

大型数据库

    → 通常外置

#### 建议使用独立虚拟环境

建议在干净的 Python 虚拟环境中执行编译和打包：

python -m venv .venv

source .venv/bin/activate

pip install -r requirements.txt

pip install cython pyinstaller

这样可以避免开发环境中无关依赖被 PyInstaller 自动发现并打入最终程序。
