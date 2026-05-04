---
source_title: VScode 搭建 React 前端开发环境
categories:
- Develop
- Tools
- Web
last_modified: '2025-03-25T07:32:17Z'
---
### INIT

#### node.js
```
 https://nodejs.cn/download/
 下载长期支持版本
```

#### VSCode
```
 https://code.visualstudio.com/Download
 zip 版本直接解压使用
```
```
 C:\Users\ldscf>node -v
 v18.17.0
```
 
```
 C:\Users\ldscf>npm -v
 9.6.7
```

以下操作可以在 VSCode 右下窗口，终端选项卡中

### SETUP
```
 npm install -g create-react-app
```

Run **npm install -g npm@9.8.1** to update!
```
 npx create-react-app XXXX
 时间大概需要 3~5分钟，生成了一个大小 230MB 的指定目录(XXXX)。除了 node_modules 目录，为 <1MB。
```

### START
```
 npm start
 在刚刚建立的项目根目录下执行
```

### Error

#### 未安装 create-react-app
```
 执行 npx create-react-app XXXX 出现如下错误
 npm ERR! code ENOENT
 npm ERR! syscall lstat
 ...
```
