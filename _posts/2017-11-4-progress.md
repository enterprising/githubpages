---
layout: post
title: 《Linux手册》- 进程
date: 2017-11-4 23:00
categories: Core
tags: [Core]
---

* content
{:toc} 
写在前面

内容整理自《Linux-Unix系统编程手册》第6章进程

# 进程和程序

可以说，进程的程序的一个实例。

程序是包含了一系列信息的文件，这些信息描述了如何在运行时创建一个进程。

这些信息主要有：

- 二进制格式标识：内核利用这东西来解释文件中的其他信息
- 机器语言指令：对程序算法进行编码
- 程序入口地址：开始执行时的起始指令位置
- 数据
- 符号表及重定位表：描述程序中函数和变量的位置及名称
- 共享库和动态链接库


从内核的角度来看，进程主要由**用户内存空间**和**一系列内核数据结构**组成。

用户内存空间包含了程序代码以及代码所使用的变量；

内和数据结构主要是用于维护进程状态消息。

# 进程号和父进程号

这里主要是连个系统调用：

1. 获取进程号

   > pid_t getpid(void);

2. 获取父进程号

   > pidt getppid(void);

 # 进程内存布局

每个进程所分配的内存由很多部分组成，通常称之为 “段”，也就是通常说的 Segment。

- 文本段：包含了进程运行的程序机器语言指令
- 初始化数据段：包含显式初始化的变量
- 未初始化数据段：未显式初始化的变量
- 栈：
  - 是个动态增长的可收缩段，由栈帧（stack frames）组成
  - 每个函数有个栈帧
  - 栈帧存储了函数的局部变量、参数和返回值
- 堆：是可在运行时为变量动态进行内存分配的一块区域

![](https://ws3.sinaimg.cn/large/006tNc79gy1fl7102ew4tj30v40zk7hk.jpg)

# 虚拟内存管理

这个应该是这一章最重要的内容了，当初学操作系统的时候也重点讲了这个。

虚拟内存的概念：

> 将程序所使用的内存切割成小型的、固定大小的 **“页”**（page）；
>
> 将RAM划分成一系列与虚存页尺寸相同的页帧。
>
> 在任意时刻，每个程序都只有部分页需要驻留在物理内存页帧中。这些页构成了所谓的 **驻留集（resident set）**
>
> 未使用的页拷贝保存在交换区（swap area）

页的概念的虚拟的，对应的实的部分其实是真实的内存空间。这中间的对应关系，靠的是内核维护的一张**页表（page table）**

页表描述了每页在进程虚拟地址空间中的位置，页表长这样：

![](https://ws2.sinaimg.cn/large/006tNc79gy1fl71gizlcbj30r20m4n2i.jpg)

虚拟内存管理使得进程的虚拟地址空间与RAM物理地址空间隔离开来。这样做有很多优点：

- 进程与进程、进程与内核互相隔离
- 便于实现内存保护机制
- 驻留集只是很小的一部分，所以程序的加载和运行都很快。又因为驻留集不大，所以RAM可同时容纳的进程数量就增大了

# 栈和栈帧

栈驻留在内存的高端并向下增长（向堆的方向）

每个用户栈帧包含的信息有：

- 函数实参和局部变量
- 函数调用的链接信息