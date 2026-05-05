---
source_title: Google C++ Style
categories:
- C++
- Develop
last_modified: '2024-10-16T09:19:48Z'
---
C++ is one of the main development languages used by many of Google's open-source projects. As every C++ programmer knows, the language has many powerful features, but this power brings with it complexity, which in turn can make code more bug-prone and harder to read and maintain.

The goal of this guide is to manage this complexity by describing in detail the dos and don'ts of writing C++ code . These rules exist to keep the code base manageable while still allowing coders to use C++ language features productively.

*Style*, also known as readability, is what we call the conventions that govern our C++ code. The term Style is a bit of a misnomer, since these conventions cover far more than just source file formatting.

Most open-source projects developed by Google conform to the requirements in this guide.

Note that this guide is not a C++ tutorial: we assume that the reader is familiar with the language.

### google cpp style

![Google_cpp_style.png](https://mwbbs.eu.org/wiki/images/8/89/Google_cpp_style.png)

### google h style

![Google_h_style.png](https://mwbbs.eu.org/wiki/images/c/c4/Google_h_style.png)

### using namespace std

有很多人不建议使用 using namespace std，还举了例子说很难区分其他比如自己写的函数到底是不是 std 中的。事实上，自己写的函数，应该有不同于 std 的命名空间，而且在使用的时候，要明显写出，如：udef::min()。

See also: https://google.github.io/styleguide/cppguide.html
