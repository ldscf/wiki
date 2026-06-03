---
source_title: Mac安装node.js
categories:
- Develop
- Web
last_modified: '2026-05-18T02:52:54Z'
---
> 安装如下工具
1. brew : macOS 包管理器, 类似于应用商店
1. fnm  : Node 版本管理器

### brew
```
 /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```
 
```
 brew -v
```

### fnm
```
 brew install fnm
```
 
```
 # setup
 echo 'eval "$(fnm env --use-on-cd)"' >> ~/.zshrc
 source ~/.zshrc
```
 
```
 fnm -v
```

由于开启了 --use-on-cd，只需在项目根目录创建 .node-version 文件（内容写 20），进入该目录时终端会自动切换 Node 版本。

### Node.js
```
 fnm install 20
 fnm install 24
```
```
 # 查看本地已安装的所有版本
 fnm list
```
 
```
 # 切换当前终端的 Node 版本
 fnm use 20
```
 
```
 # 设置全局默认的 Node 版本
 fnm default 20
```
 
```
 # 验证当前版本
 node -v
 npm -v
```
