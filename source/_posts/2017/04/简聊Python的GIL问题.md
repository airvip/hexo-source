---
title: 简聊Python的GIL问题
date: 2017-04-03 12:12:56
tags:
    - Python
---

由于通常我们用的解释器是官方实现的 CPython 。导致很多人认为 CPython 就是 Python，也就想当然的把 GIL 的锅归结为Python 语言的缺陷。

<!-- more -->

# GIL 是什么

GIL（Global Interpreter Lock）：全局解释器锁。
GIL并不是 Python 的特性，Python 完全可以不依赖于GIL。它是在实现 Python 解释器 (CPython) 时所引入的一个概念。比如：C++ 是一套语言（语法）标准，计算机并不认识这些代码，需要通过编译器来编译成可执行代码，机器才能执行。C++ 常用的编译器如：gcc，Intel C++，Visual C++等。Python 也是一样，同样的代码可以通过 CPython，PyPy，JPython 等不同的 Python解释器来解释。其中的 JPython 就没有 GIL。

我们还是来看一下官方给出的解释：

>In CPython, the global interpreter lock, or GIL, is a mutex that prevents multiple native threads from executing Python bytecodes at once. This lock is necessary mainly because CPython’s memory management is not thread-safe. (However, since the GIL exists, other features have grown to depend on the guarantees that it enforces.)
>上面的意思就是：无论你启多少个线程，你有多少个 cpu, Python 在执行的时候存在一个防止多线程并发执行机器码的 Mutex  ，同一时刻只允许一个线程运行。

# GIL 的产生

由于物理上得限制，各 CPU 厂商的**核心频率之争**已被**多核之争**所取代，也就是现在的 双核、四核等。编程语言的发展肯定也是要有效的利用 CPU 的多核特性，多线程的编程方式应运而生，随之而来的就是线程间数据一致性和状态同步的问题。

Python 作为一门如此流行的语言，为了利用多核，当然要支持多线程。而解决多线程之间数据完整性和状态同步的最简单方法自然就是加锁。 于是在 Cpython 解释器中就有了 GIL 这把大锁，开发者也潜移默化的接受了这种设定，开始大量依赖这种特性（默认 python 内部对象是 thread-safe 线程安全的，功能实现时，无需考虑额外的内存锁和同步操作）。

存在了 GIL 这把大锁，毫无疑问，效率肯定就收到了影响，大家又试图去拆分和去除 GIL ，因为保证数据安全准确性，通过 GIL 来实现是最容易的，此时再做减法找到一只替代方案就很困难了。所以简单的说 GIL 的存在更多的是历史原因。那推到重来呢？多线程的问题依然还是要解决，但是至少会比目前GIL这种方式会更优雅。

# GIL 的影响

GIL无疑就是一把全局排他锁。毫无疑问全局锁的存在会对多线程的效率有影响。甚至就几乎等于Python是个单线程的程序。
那是不是，只要全局锁释放的足够快，就能减少效率的影响呢？最差也比单线程效率高吧。理论上是这样，而实际上并没有我们想的那么乐观。

使用单线程
``` Python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

import time

def thread_run():
    n = 0
    for i in range(100000000):
        n = i + 1
    return n

if __name__ == '__main__':
    start_time = time.time()
    t1 = threading.Thread(target=thread_run)
    t1.start()
    t1.join()
    t2 = threading.Thread(target=thread_run)
    t2.start()
    t2.join()
    end_time = time.time()
    print("总共用时: {}".format(end_time - start_time))
```
执行结果
```
总共用时: 9.980383157730103
```

采用多线程，其两个线程
``` Python
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

import threading
import time

def thread_run():
    n = 0
    for i in range(100000000):
        n = i + 1
    return n

if __name__ == '__main__':
    start_time = time.time()
    t1 = threading.Thread(target=thread_run)
    t2 = threading.Thread(target=thread_run)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    end_time = time.time()
    print("总共用时: {}".format(end_time - start_time))
```
执行结果
```
总共用时: 10.032591581344604
```

注：为了减少线程库本身性能损耗对测试结果带来的影响，这里单线程的代码同样使用了线程。将多线程的异步执行改为同步执行，模拟单线程。

从上面的对比结果不难看出，我们所说的多线程的高效好像严重的大脸，那为什么单线程比多线程更快呢？这是因为 GIL 设计的缺陷。

# GIL 缺陷

Python 的线程就是 C 语言的一个 pthread，并通过操作系统调度算法进行调度。

先看下官方是怎么说的

>In general, Python offers to switch among threads only between bytecode instructions; how frequently it switches can be set via sys.setswitchinterval().

一般来讲，Python 只会在字节码指令之间的空隙去切换线程。并且多久进行一次切换是可以进行设置的，默认为5ms。

看段伪代码
```
while True:
    acquire GIL
    for i in range(1000):
        pass
    release GIL
# 此时给操作系统一个机会来切换线程
```

这种操作在单核 CPU 下毫无问题。任何一个线程被唤起时都能成功获得到GIL（因为只有释放了GIL才会引发线程调度）。但当多核 CPU 的时候，问题就来了。从伪代码可以看到，从 release GIL 到 acquire GIL 之间几乎是没有间隙的。所以当其他在其他核心上的线程被唤醒时，主线程可能又获得了 GIL 。被唤醒的线程造成了 CPU 浪费。

# 如何避免 GIL 的影响

用 multiprocess 替代，由于 GIL 而导致低效的缺陷。在 multiprocess 中采用了多线程模式。不同点在于它使用多进程而不是多线程。每个进程有自己的独立的 GIL，因此也不会出现进程之间的 GIL 争抢。

但 multiprocess 并不完美，由于线程中资源是独立的，于是又引入了 multiprocess.Queue 用来实现进程间数据通讯和同步。这一系列的操作同样会增加开销。

那用其他没有 GIL 的解析器就完美了呢？我们之前已经提到像 JPython 中就没有 GIL，由于解释器是有 Java 实现的，所以这种解释器就丢失了利用社区众多 C 语言模块有用特性的机会。毕竟功能和性能，在初期大家都会选择前者。即：Done is better than perfect。

Python GIL其实是功能和性能之间权衡后的产物，它的存在有其合理性，也有较难改变的客观因素。

是不是真的没救了？还是那句话，处理 GIL 非常困难，但是同样这种困难也是一种挑战，直面挑战是很多开发者乐此不疲的事情，其实在 Python 的每个小版本中都有 GIL 做优化。基于我目前对 Python 解释器的观察，并且没有迹象表明 GIL 在不久以后的某个时段会离开我们。

**总结**
Python 虽然利用多线程实现多核任务有问题，但可以通过多进程实现多核任务。
GIL 在较长一段时间内将会继续存在，但是会不断对其进行改进。
如果一定要通过多线程利用多核，那可以通过 C 扩展来实现，不过这样就失去了Python简单易用的特点。
