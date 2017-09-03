---
layout: post
category: "coding"
title:  "Python并行程序"
tags: [Python]
description:  "Python多线程编程"
---

## Intro
先复习一下进程和线程的区别：

### 进程：

可以理解为程序的一次执行。每一个进程在运行时，都有自己的地址空间，内存，栈数据等。进程的管理是在操作系统层面上的。进程间不能直接共享数据，只能通过通讯(interprocess communication)。

### 线程：

多个线程试运行在某个进程之中的，共享运行环境。

## Python多线程编程

### 全局解释器锁：Global Interpreter Lock (GIL)

很遗憾，我们在“冠冕堂皇”地讨论“Python多线程编程”，但事实上，Python代码是由Python虚拟机（也称作解释器主循环）来执行控制，而在虚拟机中，就做出了如下设计：在主循环中，同时只有一个线程在执行。额，好吧，这就说明，多线程是个伪命题，实际上每个线程都会单独在GIL里运行一小会。

因此，Python本身就不适合写CPU密集型程序。

## 并行

既然Python程序不支持并行的多个线程，就没有其他办法了么？当然有，开多个进程不久行了么！

### multiprocessing多进程处理包

假设我们要对某个方法'''multi_test(para)'''并行运算，就可以用下面的代码实现：
{% highlight python linenos %}
from multiprocessing import Pool
def multi_test(para):
    ...

para_list =[...] # 生成一个参数列表，用于实现参数传递

pool = ThreadPool(10) # 核心数
pool.map(multi_test, para_list) # 开始多进程

pool.close() # 关闭进程池
pool.join() # 等待进程池中的workers执行完毕
{% endhighlight %}
## 参考文献

丘恩, 宋吉广. Python 核心编程[J]. 2008.











