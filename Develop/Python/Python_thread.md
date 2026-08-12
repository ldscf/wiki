---
source_title: Python thread
categories:
- Develop
- Python
last_modified: '2023-12-26T09:43:21Z'
---
Python 多线程

### 互斥锁
1. import threading

# lock = threading.Lock()   # 生成锁对象，全局唯一
1. lock.acquire()      # 阻塞，直到获取到锁
1. lock.release()      # 释放锁

### 可重入锁

# lock = threading.RLock()   # 生成可重入锁对象，同一线程中，可多次获取锁

自动释放锁可以使用 with 方法：
```
 # with 在代码块执行前自动获取锁，结束后自动释放锁
 with lock:
```

    # code

    pass

### GIL（Global Interpreter Lock，全局解释器锁）

GIL 并不是 Python 的特性，它是 CPython 解析器所引入的一个概念。CPython 在线程执行前，必须先获得 GIL 锁，每执行 100 条字节码，解释器就自动释放 GIL 锁，让别的线程有机会执行。多线程在 CPython 中只能交替执行，即使 100 个线程跑在 100 核 CPU 上，也只能用到 1 个核。

Python解释器还有 PyPy，Psyco，JPython，IronPython 等。

### i++ 多线程不安全测试
```
 # 加锁情况下，正确累加
 GI=1000000, Time Begin=20231226171738, Time End=20231226171738, 1 secs.
 GI=10000000, Time Begin=20231226165514, Time End=20231226165514, 19 secs.
 GI=100000000, Time Begin=20231226171320, Time End=20231226171320, 167 secs.
 # not lock
 GI=3476741, Time Begin=20231226165611, Time End=20231226165611, 20 secs.
 GI=3988796, Time Begin=20231226165649, Time End=20231226165649, 20 secs.
```
```
 import time
 import datetime
 import threading
```
 
```
 # thread count
 p = 10
 gi = 0
 CS=100000
```
 
```
 tb = datetime.datetime.now()
```
 
```
 lock = threading.Lock()
 def __thread(thread_id):
```

     global gi

     for i in range(CS):

         lock.acquire()

         gi = gi + 1

         lock.release()
 
```
 threads=[]
 for i in range(p):
```

     threads.append(threading.Thread(target = __thread, args = (i,)))
 
```
 for th in threads:
```

     th.start()
 
```
 k=0
 while True:
```

     if gi + 1 > p*CS:

         break

     time.sleep(0.1)

     k = k + 1

     if k > 200:

         break
 
```
 te = datetime.datetime.now()
 sec = (te - tb).seconds
 tbs = tb.strftime('%Y%m%d%H%M%S')
 tes = tb.strftime('%Y%m%d%H%M%S')
```
 
```
 print('GI=%s, Time Begin=%s, Time End=%s, %s secs.' % (gi, tbs, tes, sec))
```
