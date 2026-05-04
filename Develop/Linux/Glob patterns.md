---
source_title: Glob patterns
categories:
- Develop
- Linux
last_modified: '2026-05-04T11:00:39Z'
---
Glob 风格模式（Glob patterns）是一种简化的、用于匹配文件路径或字符串的模式匹配语法。比正则表达式更简单易懂，常用于命令行工具、文件管理器、编程语言中的文件操作等场景。

### *

匹配任意数量的字符（包括零个字符）。
```
 *.txt 匹配所有以 .txt 结尾的文件。
 data* 匹配以 data 开头的所有文件或目录。
 *.* 匹配所有包含点号 . 的文件。
 * 匹配所有文件和目录。
```

### ?

匹配单个字符。
```
 file?.txt 匹配 file1.txt、fileA.txt、file_.txt 等，但不匹配 file.txt 或 file12.txt。
 ?.txt 匹配 a.txt, 1.txt 等
```

### []

匹配方括号内的任意一个字符。
```
 [abc].txt 匹配 a.txt、b.txt 或 c.txt。
 file[0-9].txt 匹配 file0.txt、file1.txt、...、file9.txt。
 [a-zA-Z].txt 匹配以小写或大写字母开头，.txt结尾的文件
```

### [!...][^...]

匹配不在方括号内的任意一个字符。
```
 [!abc].txt 匹配除了 a.txt、b.txt 和 c.txt 之外的任何以 .txt 结尾的文件。
 [^0-9].txt 匹配不以数字开头, .txt结尾的文件
```

### {a,b,c}

匹配花括号内的任意一个子模式。这与其他通配符不同，它不是字符级别的匹配，而是整个子模式的匹配。
```
 file{1,2,3}.txt 匹配 file1.txt、file2.txt 或 file3.txt。
 {*.txt,*.md} 匹配所有以 .txt 或 .md 结尾的文件。
 dir/{subdir1,subdir2}/* 匹配 dir/subdir1/ 和 dir/subdir2/ 下的所有文件和目录。
```

### \

转义字符。用于转义上述特殊字符，使其失去特殊含义，只代表字符本身。
```
 \*.txt 匹配名为 "*.txt" 的文件（而不是所有以 ".txt" 结尾的文件）。
 file\?.txt 匹配名为 "file?.txt" 的文件。
```

### **

递归匹配零个或多个目录。部分支持，如 bash 4+, zsh, Python(glob.glob("src/**/*.py", recursive=True)
```
 src/**/*.py 匹配 src/ 目录及其所有子目录（包括子目录的子目录）中的所有 .py 文件。 例如，它会匹配 src/main.py、src/utils/helper.py、src/ tests/unit/test_foo.py 等
 **/*.txt 匹配当前目录以及所有子目录下的 .txt 文件。
```
