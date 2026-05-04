---
source_title: Vi
categories:
- Develop
- Linux
last_modified: '2024-12-10T06:54:53Z'
---
### select-editor

Select an editor. To change later, run 'select-editor'.
1. /bin/nano ←— easiest
1. /usr/bin/vim.basic
1. /usr/bin/vim.tiny
1. /bin/ed

P.S. Don't listen to it, it's better to vi first…

### 替换

| OP | CMD | Explain |
|:---|:---|:---|
| + |
| AA -> BB | :1,$s/AA/BB/g | 从第一行替换到结尾行($) |
| AA -> BB | :%s/AA/BB/g | 全范围替换 |
| HEX: 5 | :%s/\%x05/|/g | 全范围删除不可见字符 |
| ^M | :%s/^M//g | 删除文本中的 ^M。^M 的输入方式是 Ctrl + v ，Ctrl + M，或者参考下面《输入不可见字符》条目 |
| OS | Symbol | Escape character | HEX | Explain | Memo |
|:---|:---|:---|:---|:---|:---|
| + |
| Linux | LF | \n | 0A | 换行 | Line Feed |
| Windows | LF+CR | \r\n | 0D0A | 回车换行 |
| MacOS | CR | \r | 0D | 回车 | Carriage Return |

### 输入不可见字符
```
 ^Vnnn (000 <= nnn <= 255), ^V 的输入方式是 （Windows ）
 所以上面提到的 ^M，也可以这样输入: ^V013
```

### 定位某一行
```
 10 + shift + G
```

### 行号
```
 显示: :set number
 关闭: :set nonumber
```

### 复制
```
 2yy, p
```
