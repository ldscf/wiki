---
source_title: C++ 可变参数
categories:
- C++
- Develop
last_modified: '2025-06-10T05:27:50Z'
---

### 参数默认值
 ```
std::string resp(const std::string& value, int type = 0) {
    std::ostringstream oss;
    switch (type) {
        case 0:
            oss << "$" << value.size() << "\r\n" << value << "\r\n";
            break;
        case 1:
            oss << "+" << value << "\r\n";
            break;
        case -1:
            oss << "-" << value << "\r\n";
            break;
        case -2:
            oss << "*-1\r\n";
            break;
        default:
            oss << "$-1\r\n";
            break;
    }
    return oss.str();
}
```

### va_list

stdarg.h 头文件提供了 C 语言中变长参数的功能。

在函数声明中用 ... 声明列表（必须在最后，且不能只有...）
