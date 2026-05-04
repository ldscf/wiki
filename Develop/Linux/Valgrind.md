---
source_title: Valgrind
categories:
- Linux
last_modified: '2024-11-14T02:52:05Z'
---
Valgrind 是运行在 Linux 上的一套基于仿真技术的程序调试和分析工具，用于构建动态分析工具的装备性框架。它包括一个工具集，每个工具执行某种类型的调试、分析或类似的任务，以帮助完善你的程序。Valgrind 采用模块化架构，可以容易的创建新的工具而又不会扰乱现有的结构。

| + |
| Memcheck | 检查程序中的内存问题，如泄漏、越界、非法指针等 |
| Callgrind | 检查程序中函数调用过程中出现的问题 |
| Cachegrind | 分析 CPU cache 命中率、丢失率，用于进行代码优化 |
| Helgrind | 检查多线程中的竞争问题 |
| Massif | 检查堆栈使用问题 |
| Extension | 利用 core 提供的功能，编写特定的内存调试工具 |

### 安装
```
 # apt install valgrind
 # valgrind --version
 valgrind-3.15.0
```

### 使用

#### memcheck

检测程序中出现的内存问题，检测内存读写，捕获 malloc、free、new、delete 等调用。

检测问题：
1. 使用未初始化的内存
1. 读/写释放后的内存块
1. 内存读写越界（数组访问越界/访问已经释放的内存),读/写超出 malloc 分配的内存块
1. 读/写不适当的栈中内存块
1. 内存泄漏，指向一块内存的指针永远丢失
1. 不正确的 malloc/free 或 new/delete 匹配（重复释放/使用不匹配的分配和释放函数）
1. 内存覆盖，memcpy 相关函数中的 dst 和 src 指针重叠
```
 valgrind --tool=memcheck --leak-check=full --show-leak-kinds=all --undef-value-errors=no --log-file=svr_vg.log ./XXX
```

summary：
1. definitely lost: 存在内存泄露
1. indirectly lost: 存在内存泄露，与指针结构相关
1. possibly lost: 可能存在内存泄露，如一些不符合常规的操作，例如将指针指向某个已分配内存块的中间位置
1. still reachable: 可能是没问题，存在没有释放掉一些本可以释放的内存。这种情况是很常见的，通常基于合理的理由
1. suppressed: 意味着有些泄露信息被压制了。在默认的 suppression 文件中可以看到一些 suppression 相关设置

如果二叉树的根节点被判定为 definitely lost，则其所有子节点将被判定为 indirectly lost。而如果正确修复了类型为 definitely lost 的根节点泄露，那么类型为 indirectly lost 的子节点泄露也会随着消失。

如果 possibly lost 没有造成内存上的泄露，使用 --show-possibly-lost=no 过滤该类报告信息。--show-reachable=yes 控制是否输出 still reachable 信息。
```
 *```
```

==776172== 

==776172== Process terminating with default action of signal 2 (SIGINT)

==776172==    at 0x4A6A4FF: accept (accept.c:26)

==776172==    by 0x111373: main (in /root/socket/svr)

==776172== 

==776172== HEAP SUMMARY:

==776172==     in use at exit: 13,347,333 bytes in 209,443 blocks

==776172==   total heap usage: 242,770 allocs, 33,327 frees, 21,007,508 bytes allocated

==776172== 

==776172== LEAK SUMMARY:

==776172==    definitely lost: 0 bytes in 0 blocks

==776172==    indirectly lost: 0 bytes in 0 blocks

==776172==      possibly lost: 288 bytes in 1 blocks

==776172==    still reachable: 13,347,045 bytes in 209,442 blocks

==776172==         suppressed: 0 bytes in 0 blocks

==776172== Rerun with --leak-check=full to see details of leaked memory

==776172== 

==776172== For lists of detected and suppressed errors, rerun with: -s

==776172== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)

```*
