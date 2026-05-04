---
source_title: Bootstrap
categories:
- Develop
- Web
last_modified: '2025-08-04T02:33:19Z'
---
Bootstrap 是由 Twitter 的工程师 Mark Otto 和 Jacob Thornton 于 2011 年发布的开源前端框架，最初叫 Twitter Blueprint，用于解决内部多个项目样式不统一、代码重复的问题。

### 历史
- 2011 年 8 月：Bootstrap 1 发布，HTML + CSS 工具库。
- 2012 年 1 月：Bootstrap 2 引入响应式布局。
- 2013 年 8 月：Bootstrap 3：移动优先设计、扁平化风格。
- 2016 年，几乎找不到一个没有使用 Bootstrap 的网站。
- 2018 年 1 月：Bootstrap 4：引入 Flexbox，弃用 LESS 改用 SCSS。
- 2020 年 5 月：Bootstrap 5 开始开发，去除对 jQuery 的依赖。
- 2021 年 5 月：Bootstrap 5 正式发布，彻底现代化，支持 ES6 模块、CSS 自定义属性等。
- 稳定版本：Bootstrap 5.3
- 即将推出：Bootstrap 6 正在规划中（更多关注现代 Web Components）
- 生态良好：大量第三方组件库（如 AdminLTE、MDBootstrap）
- 使用场景广泛：企业官网、后台管理系统、博客、CMS 等等

### 开发

#### 引入
```
 *```
```

<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

<link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css" rel="stylesheet">

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>

当然也可以下载到本地使用，其中 icons 本地还需要 woff 文件（见下面说明）。

# https://github.com/twbs/bootstrap/tree/main/dist/

├─ css/

│  └─ bootstrap.min.css

└─ js/
```
   └─ bootstrap.min.js
```

```*

#### 栅格系统（响应式布局核心）
 ```
  
    内容1
    内容2
  
 .container：栅格系统响应式容器，用于限制内容最大宽度并自动左右居中。在大屏幕有固定宽度（如 1140px），中小屏自动调整。
 .row：行，用于放置列。一行分为 12 栅格设计。
 .mb-4：表示给每行底部加间距 24px。还有类似：mt-? 上间距、ml-? mr-? 左右间距等。1～5 对应（4/8/16/24/48）
 .col-6：小屏列宽度定义（12 个栅格占 6 个），若不定义，默认占一行（col-12）。
 .col-md-4/8：中大屏列宽度定义（12 个栅格占 4/8 个）
 上面代码定义了一行中的两列，在大小屏中表现是不同的。小屏平分。中大屏，第一列占 1/3（4/12），第二列占 2/3
```

| 类名 | 含义 |
|:---|:---|
| col-6 | 在超小屏幕（<576px）及以上，占据 12 栅格中的 6 栅格（即 50% 宽度） |
| col-md-4 | 在中等屏幕（≥768px）及以上，占据 4 栅格（约 33.3%） |
| col-md-8 | 对应第二列，在中等屏幕及以上占 8 栅格（约 66.6%） |
**响应式表现**

| 屏幕宽度 | 内容1 宽度 | 内容2 宽度 |
|:---|:---|:---|
| 小于 768px | 各占一半（6/12） | 各占一半（6/12） |
| 大于等于 768px | 1/3（4/12） | 2/3（8/12） |

#### 常用组件

##### 按钮
 ```
主按钮
```

##### 卡片
 ```
...
```

##### 导航栏
 ```
```

##### 模态框、工具提示、警告框

##### 表单样式
 ```
  
    用户名
    
  
```

##### Bootstrap Icons

[Bootstrap 官方图标库](https://icons.getbootstrap.com)包含 1800 多个图标的免费、高质量的开源图标库。
 ```
    <!-- text-success, text-danger, text-warning,... -->
    
    
    
        body { font-size: 2rem; padding: 2rem; }
        i { margin-right: 1rem; }
    
    Bootstrap Icons Example</h1>
    
    
    
    
    
    
    
    
```

本地化使用要去其[Bootstrap Icons GitHub](https://github.com/twbs/icons/tree/main/font) 下载三个文件：
 ```
└─ font/
   ├─ bootstrap-icons.min.css
   └─ fonts/
      ├─ bootstrap-icons.woff
      └─ bootstrap-icons.woff2
```
