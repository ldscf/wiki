---
source_title: Sublime
categories:
- Doc
- Tools
last_modified: '2025-07-31T02:03:28Z'
---
Sublime Text 是一款功能强大的跨平台文本编辑器，适用于代码、标记和散文。它支持多种编程语言和标记语言，用户可以使用主题进行定制，并通过插件扩展其功能（通常是在免费软件协议下由社区构建和维护的）。

### 常用
- 查找: Command + F
- 替换: Shift + Command + F
- 列模式: Option + 上下

### Regular
1. Command + F 打开查找面板
2. 在左下侧查找面板中，点击最左侧的正则表达式图标（`.*`）（⌥⌘R）

如：想要匹配标签及之间的内容： ... 
- 贪婪模式: [\s\S]*，匹配到最后一个  标签之前
- 非贪婪模式: [\s\S]*?，匹配到第一个  标签之前

### 配置

Preferences -> settings（快捷键: Command + ,）
 ```
// Settings in here override those in "Default/Preferences.sublime-settings",
// and are overridden in turn by syntax-specific settings.
{
    "trim_trailing_white_space_on_save": true,
    "update_check": false,
    "translate_tabs_to_spaces": false,
    "word_wrap": false,
    "rulers": [80, 120],
    "ensure_newline_at_eof_on_save": true,
    "tab_size": 4,
    "fallback_encoding": "UTF-8"
}
```
1. 自动移除每一行末尾多余的空格和制表符，在离开此行焦点时进行
1. 禁止自动检查更新
1. Tab 不转换为等量的空格（写 Makefile 时会造成问题）
1. 自动换行（默认: true）
1. 在 80, 120 处显示垂直标尺，通常用来提示代码行的长度（与左下角的 Columns 值可能不同。Columns 按个数计算，一个汉字算一个，一个字母也算一个）
1. 保存文件时，文件末尾总会有一个空行，有助于版本控制系统更好地处理文件

以下默认值：
1. 制表符（Tab）缩进（默认: 4）
1. 无法确定文件编码时，使用的默认编码
