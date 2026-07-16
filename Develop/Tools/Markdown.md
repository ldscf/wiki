---
source_title: Markdown
categories:
- Doc
- Tools
last_modified: '2025-05-29T01:45:00Z'
---
Markdown 是一种轻量级标记语言，排版语法简洁，让人们更多地关注内容本身而非排版。它使用易读易写的纯文本格式编写文档，可与HTML混编，可导出 HTML、PDF 以及本身的 .md 格式的文件。因简洁、高效、易读、易写，Markdown 被大量使用，如 Github、Wikipedia 等。

### 基本语法
- 标题: 井号(#)，# 的数量代表了标题的级别
- 粗体: 前后两个星号(*)或下划线(_)
- 斜体: 前后一个星号(*)或下划线(_)
- 引用: 段落前添加一个大于(>)符号
- 列表: 段落前添加破折号(-)星号(*)或加号(+)，或添加数字并紧跟一个英文句点
- 代码: 前后反引号(`)
- 链接: [超链接显示名](超链接地址 "超链接title")，或使用尖括号()
- 图片: ![图片名称](图片链接 "图片title")
- 代码块: 前后反引号(```)或缩进至少四个空格或一个制表符
- 分隔线: 单独一行上使用三个或多个星号(***)、破折号(---)或下划线(___)
- 转义符: (\)
- 块引用嵌套: 在要嵌套的段落前添加两个大于(>>)符号
- 块引用包含其他元素: (> -, > 1)，并非所有元素都可以包含
- 保留列表连续性的同时在列表中添加另一种元素: 将该元素缩进四个空格或一个制表符

### [LaTeX](#LaTeX)
```
 $LaTex 内容$
 $\int_0^\infty e^{-x^2} dx = \frac{\sqrt{\pi}}{2}$
```

$$\int_0^\infty e^{-x^2} dx = \frac{\sqrt{\pi}}{2}$$

### [ plantUML](#UML)
 ```

## 入门
#[Markdown 基本语法](https://www.markdownguide.org/basic-syntax/)
#[Markdown 在线编辑器](https://markdown.com.cn/editor/)
```

 ```plantuml

 @startuml

 ...

 @enduml
```