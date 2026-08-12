---
source_title: 判断 Linux 应用程序是否安装
categories:
- C++
- Develop
last_modified: '2024-12-25T01:19:35Z'
---
使用 popen 执行 which 命令。
 ```
#include 
bool is_inst(const string& package_name) {
    string cmd = "which " + package_name;
    FILE* pipe = popen(cmd.c_str(), "r");
    if (!pipe) {
        return false;
    }
    char buffer[128];
    while (fgets(buffer, sizeof(buffer), pipe) != nullptr) {
        pclose(pipe);
        return true;
    }
    pclose(pipe);
    return false;
}
```

## See also
- 为什么用 while？用 if 是否可以？

< claude-3.5-sonnet >

您提出了一个很好的问题。让我解释一下为什么在这里使用while循环，以及为什么使用if语句可能不太合适。

在这个情况下，使用while循环是为了确保我们读取了管道中的所有输出。虽然在大多数情况下，我们可能只需要读取一行输出就能确定程序是否存在，但使用while循环可以处理可能出现的多行输出情况。

然而，您的观察很敏锐。在这个特定的用例中，我们其实可以简化代码，使用if语句就足够了。这是因为：
1. 我们只关心程序是否存在，而不需要处理所有的输出。
1. 如果which命令找到了程序，它通常只会返回一行输出（程序的路径）。

考虑到这一点，我们可以修改代码如下：
```
 // 使用 if 替代 while
 if (fgets(buffer, sizeof(buffer), pipe) != nullptr) {
 ...
```

这个修改版本的代码更加简洁，并且对于检查程序是否安装的目的来说已经足够了。它只检查是否能读取到至少一行输出，如果能，就认为程序已安装。

使用if而不是while的好处是：
1. 代码更简单，更容易理解。
1. 性能可能略有提升，因为我们不需要继续读取可能存在的额外输出。

总的来说，您的想法是正确的。在这种特定情况下，使用if确实是一个更好的选择。感谢您的敏锐观察！
