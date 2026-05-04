---
source_title: Java Queue
categories:
- Develop
- Java
last_modified: '2024-04-17T01:00:15Z'
---
队列(Queue)是一种非常重要的数据结构。广泛应用于多线程、网络通信、缓存、消息队列等场景。

Queue 能够高效地实现元素的插入、删除以及元素的存储，在多线程场景中安全地实现元素的读写操作。遍历操作效率较低，元素的顺序固定。

### 应用场景
- 消息队列: 在分布式系统中，消息队列是一种常用的通信方式。生产者向队列中添加消息，消费者从队列中取出消息进行处理。如：ActiveMQ、RabbitMQ 等消息队列服务均使用了队列的相关技术
- 线程池: 通常使用 BlockingQueue 来存储待执行的任务。当线程池中的线程已经全部分配任务时，新的任务将会被阻塞，直到有线程可用为止。如：ThreadPoolExecutor 使用 BlockingQueue 来存储待执行的任务
- 缓存: 通常使用 LinkedList 或 ConcurrentLinkedQueue 来实现。缓存存储的是最近访问过的元素，当缓存已满时，新的元素会将最旧的元素移除

### 接口实现类

Queue 接口继承自 Collection 接口，添加了一些队列相关的方法。Queue 的实现类有多种，包括 LinkedList、ArrayBlockingQueue、PriorityQueue、ConcurrentLinkedQueue 等。
- LinkedList: 双向链表实现类。实现了 Deque 接口(Queue接口的子接口)，因此 LinkedList 可以被视为 Queue 的一种实现。插入、删除操作效率高
- ArrayBlockingQueue: 基于数组的有界阻塞队列实现类，长度固定的环形数组。具有先进先出的队列特性，多线程安全，可以指定等待时间，当队列满或空时，线程将被阻塞(put)
- PriorityQueue: 基于堆结构的带优先级的队列实现类。根据元素的排序规则进行排序，每次出队时都会返回当前队列中最小(或最大)的元素。插入、删除效率高
- ConcurrentLinkedQueue: 基于链表的无界并发队列实现类。多线程安全，能够高效地处理高并发场景。不支持阻塞操作，不保证元素的排序

### 类方法
- offer(E e): 将元素e添加到队列尾部，返回值(true/false)表示添加操作是否成功(add)
- poll(): 移除队列头部的元素并返回该元素，如果队列为空，则返回 null(remove)
- peek(): 返回队列头部的元素，如果队列为空，则返回 null(element)

### 多线程
- ArrayBlockingQueue 中，isEmpty() 并不是一个可靠的退出条件，因为在多线程环境下，可能会遇到竞态条件的问题。
