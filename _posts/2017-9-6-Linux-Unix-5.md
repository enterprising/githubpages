---
layout: post
title: 《Linux/Unix系统编程手册》-第五章 深入探究文件I/O
date: 2017-10-6 20:00
categories: Core
tags: [Core]
---

* content
{:toc} 
所谓的深入研究，大概就是在上一章的基础上，对于文件的操作引入了 “原子性” 这一概念。如果你对多线程和线程安全那些有了一定的了解的话，理解起来这个就会非常简单了。

# 原子操作和竞争条件

所有系统调用都是以 **原子** 操作的方式执行的。

对于这句话的进一步说明：内核保证了某系统调用中的所有步骤会作为独立操作而一次性加以执行，期间不会被其它进程或者线程所中断。

原子操作解决了**竞态状态**的问题 - - 两个进程或者线程操作一个共享资源，解决不可预期（因为，鬼知道它们谁先谁后，而且一个改了对另一个会有影响）.. 简单的说就是，这两个进程或者线程获得CPU使用权的先后相对顺序。

## 以独占方式创建一个文件

如果两次以 O_WRONLY 方式 open()文件的话，会报错。

## 向文件尾部追加数据

用write()的话，也有可能会出现多个进程/线程想向同一个文件追加数据。此时会出现竞态状态。

使用 O_APPEND 标志，就能将文件偏移量的移动和数据的写操作纳入同一原子操作。

# 文件控制操作：fcntl()

> int fcntl(int fd, int cmd, ….);

cmd参数范围很广，随后细述。

第三个参数可省略，内核会根据cmd参数（如果有的话）的值来确定该参数的数据类型。

# 打开文件的状态标志

fcntl() 的用途之一：针对一个打开的文件，获取或修改其访问模式和状态标志（open的flag参数）。

## F_GETFL

获取那些设置，需要将 fcntl() 的 cmd 参数设置为：**F_GETFL**。

demo：

```c
int flags,accessMode;
flags = fcntl(fd, F_GETFL);
if(flags == -1)
  	errExit("fcntl");
```

在上述代码之后，可以用下面这段代码测试文件是否以同步写的方式打开

```c
if(flags & O_SYNC)
  	printf("writes ars synchronized.");
```

判断访问模式：O_RDONLY(0)、 O_WRONLY(1)、 O_RDWR(2)，有点复杂。并不能向上面判断是否同步直接 & 那样，所以，需要根据掩码 O_ACCMPDE 与flag 相与。

```c
accessMode = flags & O_ACCMODE;
// 判断方式，另外两种类似
if(accessMode == O_WRONLY){
    // 可写
}
```

## F_SETFL

更改那些设置，需要用 **F_SETFL**。

允许修改的标志有：O_APPEND、O_NONBLOCK、O_NOATIME、O_ASYNC、O_DIRECT。

使用 fcntl() 修改文件状态标志，适用于以下场景：

- 文件不是由调用程序打开的，所以程序也无法使用 open() 系统调用来控制文件的状态标志；
- 文件描述符的获取是通过 open() 之外的系统调用。比如 pipe() 系统调用等。

demo：

```c
int flags;
flags = fcntl(fd, F_GETFL);
if(flags == -1)
  errExit("fcntl");
flags = flags|O_APPEND;
if(fcntl(fa, F_SETFL, flags) == -1)
  errExit("fcntl");
```

# 文件描述符和打开文件之间的关系

文件描述符 fd 和 打开的文件之间，并不是一一对应的关系。

多个文件描述符指向同一打开文件。这些文件描述符可在相同或不同的进程中打开。

为了理解这东西，我们需要知道，内核维护了三个数据结构：

- 进程级的文件描述符表；
- 系统级的打开文件表；
- 文件系统的 i-node 表；

## 文件描述符表

针对每个进程，内核为其维护打开**文件的描述符表**，这表的每一条都记录了单个文件描述符的相关信息：

- 控制文件描述符的一组标志；
- 对打开文件句柄的引用；

## 系统级的文件表

系统级的描述表（又叫**文件表**），将表中的各条目称为文件句柄（handle）。句柄存储了与一个打开文件相关的全部信息：

- 当前文件偏移量；
- 打开文件时所使用的状态标志；
- 文件访问模式（只读、只写、读写）；
- 对信号驱动 I/O 相关的设置；
- 对该文件 i-node 对象的引用；

## i-node 表

每个文件系统都会为驻留其上的所有文件建立一个 **i-node 表**。上面主要的信息有：

- 文件类型（比如，常规文件、Socket、管道等）和访问权限；
- 一个指针，指向该文件持有的锁的列表；
- 文件的各种属性，包括文件大小以及不同类型操作相关的时间戳等。

## 三者的关系

![](https://ws4.sinaimg.cn/large/006tNc79ly1fk8vkvbdgaj30z40oe7iw.jpg)

说明：

- 两个不同的文件描述符，若指向同一打开文件句柄，将共享同一文件偏移量。。因此，两个描述符，一个改了文件，另一个也可以看到。不管这两个描述符在不在同一个进程，这条都满足。
- 要获取和修改打开的文件标志，可以执行 fcntl() 的F_GETFL和F_SETEL操作。
- 文件描述符标志（close-on-exec标志），为进程和文件描述符所私有。改了一个，并不会影响这个进程或者其它进程下面的其它文件描述符。

# 复制文件描述符

## 2>&1

shell的 I/O 重定向语法： 2>&1，用于通知shell吧标准错误（文件描述符2）重定向到标准输出（文件描述符1）。

因此，下列命令将把标准输出和标准错误写入到 result.log 文件：

> $ result.log 2>&1

因为实现了重定向，因此文件描述2 与文件描述符1 指向同一个打开文件句柄。可以调用 dup()和dup2()来实现。

## dup、dup2

dup() 调用 复制一个打开的文件描述符 oldfd，并返回一个新描述符，两者都指向同一打开的文件句柄。系统会保证新的符号描述符移动是编号值最低的未用文件描述符。

> int dup(int oldfd);
>
> // return new file descriptor on success,or -1 on error.

dup2() 系统调用会为oldfd 参数所指定的文件描述符创建副本，其编号由 newfd 参数指定。

> int dups(int oldfd, int newfd);
>
> // return new file descriptor（就是newfd） on success,or -1 on error.

如果由 newfd 参数所指定编号的文件描述符之前已经打开，那么dup2()会首先将其关闭。

## fcntl()的 F_DUPFD

> newfd = fcntl(oldfd, F_DUPFD, startfd);

该调用为 oldfd 创建一个副本，且将使用大于等于 startfd 的最小未用值作为描述符编号。

## dup3

dup3() 系统调用完成的工作与 dup2() 相同，只是新增了一个附加参数 flag，这个参数是可以修改系统调用行为的掩码。

> int dup3(int oldfd, int newfd, int flags);

dup3是Linux特有的。同时值支持一个标志：O_CLOEXEC。

# 在文件偏移量处的 I/O：pread()和 pwrite()

