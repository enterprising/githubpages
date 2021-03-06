---
layout: post
title: 《Linux/Unix系统编程手册》-第三章 系统编程
date: 2017-10-2 15:00
categories: Core
tags: [Core]
---

* content
{:toc} 
其实我一直都对系统编程有着极大的兴趣.. 因为感觉，只有懂更底层的东西，才能算是真正脱离了码农的头衔，进阶到程序员..

# 系统调用

系统调用是受控的内核入口。

进程可以通过这个机制，要求内核以自己的名义去执行某些动作，这其中包括创建新进程、执行I/O，以及为进程间通信创建管道等。

深入理解系统调用的动作方式之前，需要关注以下几点：

- 系统调用将处理器从用户态切换到核心态，以便CPU访问受到保护的内核内存；
- 系统调用的组成是固定的，每个系统调用都由一个唯一的数字来标识。（程序通过名称来标识系统调用，对这一编号往往一无所知）
- 每个系统调用可辅之一套参数，对用户空间（也叫进程的虚拟地址空间）与内核空间之间传递的信息加以规范。

其实系统调用和C语言的函数调用很类似。

以 x86-32为例，执行系统调用时，幕后经历的步骤有：

- 应用程序通过调用C语言函数库中的外壳（wrapper）函数，来发起系统调用；
- 对系统调用中断处理来说，外壳函数必须保证所有的系统调用参数可用。通过堆栈，这些参数传入外壳函数，但内核却希望将这些参数置入特定寄存器。因此，外壳函数会将上述参数复制到寄存器；
- 由于所有系统调用进入内核的方式相同，内核需要设法区分每个系统调用。为此，外壳函数会将系统调用编号复制到一个特殊的CPU寄存器（%eax）中；
- 外壳函数执行一条中断机器指令（int 0x80），引发处理器从用户态切换至核心态；
- 为了响应中断 0x80，内核会调用 system_call()例程来处理这次中断，具体流程如下：
  - 在内核栈中保存寄存器值；
  - 审核系统调用编号的有效性；
  - 执行特定的任务，然后将结果状态返回给 system_call()例程；
  - 从内核栈中恢复各寄存器值，并将系统调用返回值置于栈中；
  - 返回至外壳函数，同时处理器切换回用户态。
- 如果系统调用服务例程的返回值表明调用有误，那么外壳函数会使用该值来设置全局变量 errno 。然后，外壳函数会返回到调用程序，并同时返回一个整数值，以表明系统调用是否成功。

附图：

![屏幕快照 2017-10-02 下午4.33.21](https://ws4.sinaimg.cn/large/006tKfTcly1fk3zjvkxhpj30qo0qkacx.jpg)

# 标准C语言库；GNU C语言函数库（glibc）

GUN C语言的函数库是Linux上最常用的实现。

。。然后研究glib去了，晚点再继续写。

好了，还在下，Mac装这东西好像有点麻烦。感觉成功一半了，先继续看书吧。

一般的Linux系统，确定系统的glibc版本：

> $ /lib/libc.so.6

坑爹啊.. 我这边是mac os.. 

# 处理来自系统调用和库函数的错误

几乎每个系统调用和库函数都会返回一个状态值。

## 处理系统调用错误

一般情况下，返回值为 -1 时表示出错。

系统调用失败时，会将全局整形变量**errno**设置为一个正值，以标识具体的错误。

系统调用失败后，常见的做法之一是根据 errno 值打印错误信息，这时候可以用 perror()和strerror()。

perror 会打印出其 msg 参数所指向的字符串，紧跟一条与当前 errno 值相对应的信息。

strerror 会针对其参数 errnum 参数中所给定的错误号，返回相应的错误字符串。

## 处理来自库函数的错误

不同的库函数在调用发生错误时，返回的数据类型和值也各不相同。从错误处理的角度看，库函数主要分为：

- 有些库函数返回错误信息方式与系统调用返回相同---都是-1；
- 有些库函数在出错时会返回 -1 之外的其他值，但仍会设置 errno 来表明具体的出错情况；
- 有些根本不使用 errno，对于这一类，确认错误存在与否的方法各不相同，需要查手册，但不应该使用 errno、perror()或strerror()来诊断。

# 可移植性问题

这里主要讲了各种宏，包括特性测试宏；

各种系统数据类型，包括有符号整形，无符号整形这种。。嗯，后面会详细的描述。

# 总结

系统调用允许进程向内核请求服务。与用户空间的函数调用相比，哪怕是最简单的系统调用都会产生巨大的开销，因为执行系统调用，需要从用户态切换到核心态。这中间内核需要验证各种东西，而且还存在着各种信息的传递。

C语言标准库的实现，Linux下一般用 glibc。

大多数系统调用都会有个返回值，这个非常非常重要。

