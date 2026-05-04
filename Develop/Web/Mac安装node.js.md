---
source_title: Mac安装node.js
categories:
- Develop
- Web
last_modified: '2024-01-24T08:17:00Z'
---
安装如下工具
1. brew
1. nvm
1. node.js

### brew
```
 /bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
 # 不需要安装 Core、Cask、services
 brew -v
```

### nvm
```
 export HOMEBREW_NO_INSTALL_CLEANUP=TRUE
 brew cleanup nvm
 brew install nvm
 echo "source $(brew --prefix nvm)/nvm.sh" >> .zsh_profile
 . ~/.zsh_profile
 nvm -v
```

### node.js
```
 nvm install 12.18.0 
 nvm list
```
