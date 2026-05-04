---
source_title: VSCode VS PyCharm
categories:
- Develop
- Python
- Tools
last_modified: '2025-03-25T07:28:36Z'
---
选 VSCode 的，有个非常重要的原因是——远程开发。

### 远程开发模式

#### PyCharm 传统的远程开发模式
- 编码工作在 Windows 下用 PyCharm 完成，然后用 SVN/SFtp 同步至远程服务器。然后调试、打包，过程中用 Putty 或者 vi 做一些小的修改
- 或者用 Mobaxterm/XShell 代替 Putty，将远程服务器下的窗口投影到本地（远程服务器上部署 PyCharm + Python）

#### VSCode 远程开发模式
- VSCode 通过 SSH 连接到远程服务器，实时性更好。(Remote Development 插件)

### 其他
- PyCharm Pro 比 VSCode 好用很多，社区版跟 VSCode 比各擅胜场
- PyCharm 支持三路归并，VSCode 1.7.1 之后，支持 3-way merge

#### VSCode
- 算法领域用 VSCode 更好，它对 jupyter notebook 的支持很好。可以随时运行任何一个 py 文件中的任意一段代码（需要初始化代码）
- 如果想看某个函数、某段代码是否有错，运行效果如何，可以使用 Pytest 快速加上一段测试代码来驱动这个函数（代码）来运行
- VSCode 在 SSH/Sftp 方面较为方便，PyCharm 要设路径映射，且只能上传，无法调用远程解释器
- VSCode 的代码补全功能插件(kite)，速度和与 PyCharm 相当

##### 弱
- 代码重构。VSCode + pylance 在重构、甚至是简单的代码 formatting 上，有时都能出严重的bug（据github issue,这里有race condition)。
- 不支持自动移除 unused imports
- 单元测试 vscode 偶尔会无法发现测试用例，如果测试用例编译有问题的话

#### PyCharm
- PyCharm 在排版上表现较好，勾勾选选就能看排版效果
- PyCharm 用来看 dataframe 很好，VSCode 这方面也差很多
- PyCharm 在 datagrip 的功能，VSCode 目前还没有插件能比。

##### 弱

PyCharm 无论哪个版本，都比 VSCode 要多占资源

PyCharm 社区版不支持远程调试（连 wsl 也不支持）。如果倾向于 Windows + Linux 的混合式开发，即 IDE 运行在 Windows上，但 interpreter 运行在 Linux 机器上（或者 WSL 中）
