---
source_title: CSS
categories:
- Develop
- Web
last_modified: '2025-07-18T02:24:47Z'
---
CSS（Cascading Style Sheets，层叠样式表）于 1996 年由 W3C（万维网联盟）推出，旨在将网页的内容（HTML）与表现（样式）分离。在 CSS 出现之前，网页设计主要依赖 HTML 的标签和属性，这导致内容结构混乱，维护困难。

CSS 的诞生标志着网页样式管理的专业化和标准化，为网页设计带来了巨大的灵活性和可维护性。

### 功能

CSS 主要用于定义网页中 HTML 元素的视觉样式：
1. 样式控制：可以设置字体、颜色、边距、边框、背景等
1. 布局设计：配合 Flexbox、Grid 等布局模型实现响应式设计
1. 响应式设计：通过媒体查询适配不同设备屏幕（手机、平板、PC）
1. 动画与过渡：实现元素的动态效果，提高用户体验
1. 组件化与复用：通过类名、选择器等机制实现样式复用，提升开发效率

### 现代 CSS 的发展趋势
1. CSS 变量（Custom Properties）：提高样式可维护性
1. Flexbox 与 Grid：现代布局首选方案
1. 模块化与预处理器（如 SCSS、LESS）：支持更复杂的结构与逻辑
1. Tailwind CSS、Bootstrap 等框架：提升开发效率，快速搭建 UI
1. Scoped CSS 与 CSS-in-JS（如 styled-components）：适用于组件化开发

### CSS 选择器

| 选择器类型 | 示例 | 描述 | 用途 | 优先级 |
|:---|:---|:---|:---|:---|
| .class | 类选择器 | .title | 匹配 class="title" 元素 | 用于组件样式定义，多个元素共享样式 | 10 |
| #id | ID 选择器 | #main | 匹配 id="main" 元素（页面中唯一） | 主要用于唯一元素，如页面顶部导航、主容器等 | 100 |
| element | 元素选择器 | p | 匹配所有 <p> 标签 | 用来对 HTML 标准标签重定义，设定默认样式 | 1 |
| a:hover | 伪类选择器 | a:hover | 鼠标悬停在链接上时生效 | 用于交互状态样式，如悬停、聚焦、选中等 | 10，相加 |
| 组合选择器 | div p | 选中在 div 内部的所有 p 元素 | 精细控制嵌套关系中的元素样式 | 1，相加 |
| 属性选择器 | input[type="text"] | 选中 type 为 text 的 input | 用于精确控制具有特定属性的元素样式 | 10+ |
| 伪元素选择器 | p::first-line | 选中段落的第一行 | 用于样式化部分内容，如首行、首字母等 | 1 |
行内样式（style="…"）优先级为 1000（最高）。

### 语法
 ```
选择器 {
     属性1: 值1;
     属性2: 值2;
   }
```

### 示例
1. 类选择器：为所有 .card 添加边框、圆角和内边距
1. ID 选择器：让 #page-title 居中显示，字体大小为 24px
1. 元素选择器：设置所有 <p> 段落的字体大小为 14px，行距为 1.6
1. 伪类选择器：当鼠标悬停在 .read-more 上时，链接字体变为蓝色，背景变为浅蓝色
1. 组合选择器：让所有 .card 中的 .title 字体加粗、颜色为 darkblue

# 属性选择器（加分项）：设置所有 href="#" 的  链接字体为红色
1. 伪元素选择器（加分项）：为 .content 添加首行缩进

#### CSS
```
 *```
```


/* 1. 卡片边框和内边距 */

.card {

  border: 1px solid #ccc;

  border-radius: 8px;

  padding: 16px;

  margin: 10px auto;

  width: 60%;

}

/* 2. 页面标题 */

#page-title {

  text-align: center;

  font-size: 24px;

}

/* 3. 所有段落 */

p {

  font-size: 14px;

  line-height: 1.6;

}

/* 4. 鼠标悬停 */

.read-more:hover {

  color: blue;

  background-color: #e0f0ff;

}

/* 5. 卡片中的标题样式 */

.card .title {

  color: darkblue;

  font-weight: bold;

}

/* 6. 属性选择器 */

a[href="#"] {

  color: red;

}

/* 7. 首行缩进 */

.content::first-line {

  text-indent: 2em;

}

```*

#### HTML
```
 *```
```

<!DOCTYPE html>

<html lang="zh-CN">

<head>

  <meta charset="UTF-8" />

  <title>CSS 选择器练习</title>

  <style>

    /* 练习区域：在这里写 CSS */

  </style>

</head>

<body>

  <h1 id="page-title">文章列表</h1>

  <div class="card">

    <h2 class="title">第一篇文章</h2>

    <p class="author">作者：张三</p>

    <p class="content">这是一段文章内容的摘要，欢迎阅读！</p>

    <a href="#" class="read-more">阅读全文</a>

  </div>

  <div class="card featured">

    <h2 class="title">第二篇文章</h2>

    <p class="author">作者：李四</p>

    <p class="content">第二篇文章的内容摘要，精彩继续。</p>

    <a href="#" class="read-more">阅读全文</a>

  </div>

</body>

</html>

```*

### 常用 CSS

#### 超长行省略
```
 *```
```

/* 超长行信息显示省略号 */

.info {

  display: inline-block;      /* 排在一行内 */

  max-width: 32ch;            /* 最大宽度 32 个字符 */

  overflow: hidden;           /* 当内容溢出容器时，隐藏超出的部分（不换行） */

  text-overflow: ellipsis;    /* 当文字太长时，末尾用 ... 显示省略部分 */

  white-space: nowrap;        /* 禁止换行 */

  font-size: 10px;            /* 字体大小 */

  vertical-align: middle;     /* 垂直居中对齐 */

  margin-left: 6px;           /* 左边距 */

  margin-right: 6px;          /* 右边距 */

}

```*
* 1 ch 指的是当前字体中 “0” 的宽度
* 1 em 指的是当前字体宽度（如： font-size: 16px）
* 1 px 指的是 1 个像素

也可以这样：max-width: min(32ch, 18em, 240px);

#### 图片显示
```
 *```
```

/* 图片显示 - 自动拉满父容器宽度 */

.cover-img {

  height: 160px;              /* 图片高度为 160px */

  width: 100%;                /* 图片自动拉满父容器宽度 */

  object-fit: cover;          /* 填满容器方式裁剪，保留比例 */

  border-radius: 6px;         /* 圆角样式 */

}

```*
