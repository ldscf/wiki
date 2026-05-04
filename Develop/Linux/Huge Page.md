---
source_title: Huge Page
categories:
- Linux
last_modified: '2024-12-25T08:56:22Z'
---
Huge Page(大页)是一种内存分配方式，将物理内存划分为比普通页更大的内存块。与传统的 4KB 普通页管理方式相比，HugePages 更适合管理大内存（如 8GB 以上），可以显著提高内存管理效率和应用程序性能。还可以减少TLB（Translation Lookaside Buffer）缺失，提高内存访问效率，尤其适用于大内存连续访问的应用，如数据库、大数据处理等。

如在 Linux 平台安装中，Oracle 建议使用 HugePages 获取 Oracle 数据库的最佳性能。如果在群集成员节点上配置了 HugePages，GIMR 系统全局区域（SGA）将安装到HugePages 内存中。GIMR SGA 最高占用 1 GB 的 HugePages 内存。
1. HugePage 在操作系统启动期间被动态分配并被保留
1. 对于 Oracle 服务器，把 Hugepage 设置成 SGA(所有 instance SGA 之和)大小即可

透明大页 (Transparent Huge Pages):
1. 内核自动转换: Linux 内核从 2.6.32 开始引入了透明大页（THP）功能，它会自动将连续的 4KB 小页合并成大页，以提高内存访问效率
1. 自动触发: 当系统检测到有连续的 4KB 小页被频繁访问时，内核会自动将它们合并成大页，而不需要手动配置
1. AnonHugePages 的影响: 即使系统没有明确配置大页，但由于 THP 的存在，仍然可能出现 AnonHugePages

| 参数 | 说明 |
|:---|:---|
| + |
| AnonHugePages | 透明大页的使用量，即没有对应文件映射的大页 |
| HugePages_Total | 系统中预留的 HugePages 的总数 |
| HugePages_Free | 尚未分配的 HugePages 数量 |
| HugePages_Rsvd | 已被应用程序分配但尚未使用的 HugePages 数量 |
| HugePages_Surp | 配置修改后多余的 HugePages 数量 |
| Hugepagesize | 每个 HugePage 的大小 |
 ```

# cat /proc/meminfo | grep Huge
 AnonHugePages:   2519040 kB
 HugePages_Total:       0
 HugePages_Free:        0
 HugePages_Rsvd:        0
 HugePages_Surp:        0
 Hugepagesize:       2048 kB
```

### 禁用

临时禁用
```
 echo never > /sys/kernel/mm/transparent_hugepage/enabled
```

永久禁用
```
 在内核启动参数中添加 transparent_hugepage=never
```

路径：
```
 # Redhat
 /sys/kernel/mm/redhat_transparent_hugepage/enabled
 # Debian
 /sys/kernel/mm/transparent_hugepage/enabled
```
