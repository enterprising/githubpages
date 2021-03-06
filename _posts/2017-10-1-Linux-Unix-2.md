---
layout: post
title: 《Linux/Unix系统编程手册》-第二章 基本概念
date: 2017-10-1 9:00
categories: Core
tags: [Core] 
---

* content
{:toc}
# 操作系统的核心-内核

主要是指管理和分配计算机资源（即CPU、RAM和设备）的核心层软件。

内核的职责：

1. 进程调度：计算机内有一个或者多个CPU，以执行程序指令。Linux属于抢占式，抢占是指一组规则，这组规则控制着哪些进程获得对CPU的使用，以及每个进程能使用多长的时间。这两者都是由内核来控制的。
2. 内存管理
3. 提供了文件系统
4. 创建和终止进程
5. 对设备的访问
6. 访问Internet
7. 提供系统调用API

内核态和用户态

​    在用户态下运行时，CPU只能访问被标记为用户空间的内存，不能访问内核空间的内存；

​    在内核态下运行时，CPU既能访问用户空间内存，又能访问内核空间内存。

以进程及内核视角检视系统

​    进程不知道自己对CPU的占用何时到期，也不知道接下来会是哪个进程过来抢自己的CPU，更不能与计算机外接的输入输出设备直接通信。   但，内核可以。内核对系统的一切无所不知无所不能。

# shell

​    shell是一种具有特殊用途的程序，主要用于读取用户输入的命令，并执行相应的程序以响应命令。又叫命令解释器。

​    设计shell的目的不仅仅是用于人机交互，对于shell脚本进行解释也是其用途之一。

# 用户和组

​    系统会对每个用户的身份做唯一标识，用户可隶属于多个组。

用户：

 - 系统的每个用户都拥有唯一的登录名（用户名）和与之对应的用户ID（UID）
 - 还有组ID：用户所属第一个组的整数型组ID
 - 主目录：用户登录后锁居于的初始目录
 - 登录shell：执行以解释用户命令的程序名称

组：

- 为了方便管理，控制对文件和其他资源的访问，所以将用户进行分组
- 有组名、组ID、和用户列表等信息

超级用户：

ID为0，登录名一般为 root。可以访问系统中的任何文件，也能发送信号干预系统运行的所有用户进程。

# 单根目录层级、目录、链接及文件

单根目录，这个是Linux和Windows一个非常大的不同点。Linux的目录层级的根基就是名为 “/” 的根目录。

文件类型：

嗯，有点多。

路径和链接：

路径有绝对路径和相对路径啥的，链接的话是指“文件名+引用”。

符号链接：

类似于普通链接，符号链接给文件起了个“别名”。一个符号链接对应着目录中内容为“文件名+指针”的一条记录，而指针指向的文件内容为另一个文件名的符号串。嗯，总之就是递归的找下去。

文件名：

大多数情况下最长能达到255个字符。除了“/”和空字符“\0”之外的所有字符，都可以用。但尽量避免“-”开头，因为可能会被shell误认为是命令行选项开关。

路径名：

绝对路径：**以“/”开始，指明文件相对于根目录的位置**。

相对路径：**跟绝对路径比的话，缺少了起始的“/”。**

当前工作目录：

是进程解释相对路径的参照点，是单根目录层级下进程的“当前位置”。

文件的所有权和权限：

主要就是可读可写那些。

# 文件I/O模型

Unix系统I/O模型最为显著的特性之一就是其IO通用性概念。也就是说，同一套系统调用open(),read(),write(),close()等所执行的IO操作，可施于所有文件类型，包括设备文件。

就本质而言，内核只提供一种文件类型：字节流序列。在处理磁盘文件、磁盘时，可通过``lseek()``系统调用来随机访问。

文件描述符：

描述符0为标准输入，指代为进程提供输入的文件；

描述符1为标准输出，指代供进程写入输出的文件；

描述符2为标准错误，指代供进程写入错误信息或异常通告的文件。

这三种描述符和文件流的 stdin、stdout、stderr向对应（stdio函数库）。

stdio函数库：

C编程语言在执行文件IO操作时，往往会调用C语言标准库的IO函数。也将这样一组IO函数称为stdio函数库，这里面就包括用得非常多的fopen() fclose() scanf() printf() fgets() fputs()等。

stdio函数位于IO系统调用层（open(),read(),write(),close()等）之上。

# 程序

程序主要是指源码，或者经过编译和链接处理的机器码。

过滤器：

从stdin读取输入，加以转换，再将转换后的数据输出到 stdout。拥有这种行为的程序叫过滤器，cat、grep、tr、sort、wc、sed、awk等。

命令行参数：

即程序运行时在命令行中输入的内容。要访问命令行参数，main函数就要做出如下声明：

``int main(int argc,char * argv[])``

argc 变量包含命令行参数的总个数，argv 指针数组的成员指针则逐一指向每个命令行参数字符串。argv[0]，标识程序名本身。

# 进程

进程就是正在执行的程序实例。

执行程序时，大概的步骤：

- 首先，内核将程序代码载入虚拟内存；
- 为程序变量分配空间；
- 建立内核记账（bookkeeping）数据结构，记录和进程相关的信息（比如，进程ID，用户ID，组ID以及中止状态等）；

内核主要就管理一下进程的资源啥的，这里还涉及到资源的共享。

## 进程的内存布局

逻辑上将一个进程划分为以下几部分（也称为段）。

- 文本：程序的指令；
- 数据：程序使用的静态变量；
- 堆：程序可从该区域动态分配额外内存；
- 栈：随函数调用、返回而递增的一片内存，用于为**局部变量和函数调用链接信息**分配存储空间。

## 创建进程和执行程序

进程可使用系统调用 fork() 来创建一个新的进程。

调用 execve() 去加载并执行一个全新的程序，这个函数会销毁现有的文本段、数据段、堆段和栈段，然后创建新的。

在C语言库里，除了execve之外，基本上以 exec 开头的都和执行有点点关系。

## 进程ID和父进程ID

每个进程都有唯一的一个进程标识符（PID），此外每个进程还有一个父进程标识符（PPID）属性，用以标识请求内核创建自己的进程。

## 进程终止和终止状态

终止进程的方式：

- 使用_exit()之类的系统调用，请求退出；
- 向进程传递信号，将其kill

无论以上面哪种方式退出，进程都会生成”终止状态“。一般情况下，终止状态为0表示正常退出，非0表示不正常，有错误。

## 进程的用户和组标识符（凭证）

每个进程都有一组这样的东西

- 真实用户ID和组ID：用来表示进程所属的用户和组。
- 有效用户ID和组ID：进程在访问受保护资源时，会使用这两个ID来确定访问权限。
- 补充组ID：用来标识进程所属的额外组。

## 特权进程

。。就是用户ID为0的进程，通常由内核所施加的权限限制对此类进程无效。

## 能力

把传统赋予超级用户的权限划分为一组互相独立的单元，称之为”能力“。

赋予某进程部分能力，使得其既能执行某些特权级操作，又防止其执行其它特权级操作。

简单的说就是解耦嘛。

## init进程

系统引导时，内核会创建一个名为init的特殊进程，及”所有进程之父“。

init进程的进程号为1，且总是以超级用户权限运行，谁都杀不死它，包括root用户也不行。只有关闭系统才能终止该进程。

init的主要任务是创建并监控系统运行所需的一系列进程。

## 守护进程

这个已经非常熟了。。想想Java的GC就知道了。

主要特性：

- 长生不老，守护进程通常在系统引导时启动，直至系统关闭前，会一直都活着；
- 守护进程在后台运行，且没有控制终端供其读取或写入数据。

例子的话主要有：syslogd(在系统日志汇总记录消息)和httpd（例题http分发web页面）

## 环境变量

绝大多数shell中，可使用export命令来创建环境变量，比如：

> $ export JAVA_HE = 'hello world'

## 资源限制

使用系统调用 setrlimit()，进程可为自己消耗的各类资源设定一个上限。

这类资源限制的每一项都有两个相关值：

- 软限制：限制了进程可以消耗的资源总量；
- 硬限制：软限制的调整上限；

由fork()创建的新进程，会继承其父进程对资源限制的设置。

ulimit() 可以调整shell的资源限制。

# 内存映射

调用系统函数 mmap() 的进程，会在其虚拟地址空间中创建一个新的内存映射。

映射主要分为两类：

- 文件映射：将文件的部分区域映射入调用进程的虚拟内存。映射一旦完成，对文件映射的内容的访问则转换为对相应区域的字节操作。映射页面会按需自动从文件中加载。
- 相映成趣的是并无文件与之相对应的匿名映射，这时候其映射页面的内容会被初始化为0。

# 静态库和共享库

所谓的目标库的话，我觉得可以理解成类似于工具类的存在。

## 静态库

静态库是对已编译目标模块的一种结构化整合。要使用静态库中的函数，需要在创建程序的链接命令中指定相应的库。主程序会对静态库中隶属于各目标模块的不同函数加以引用。

链接器在解析了引用情况后，会从库中抽取所需目标模块的副本，将其复制到最终的可执行文件中，这就是静态链接。

静态链接的问题主要有两个：

1. 复制了副本，所以造成了**磁盘空间的浪费**；
2. 调用同一库函数的程序，若均已静态链接方式生成，且又于同时加以执行，这回造成**内存的浪费**。因为每个程序所调用的函数都有一份副本驻留在内存中。

## 共享库

设计的目的是为了解决静态库的问题。

经过编译处理的函数仅在共享库内保存一份，所以节约了磁盘空间。

# 进程间通信及同步

这东西... 我研究挺久的了。

这东西简称叫做 IPC 。

主要机制有：

- 信号：用来表示事件的发生；
- 管道和FIFO：用于在进程间传递数据；
- 套接字：共同一台主机或是联网的不同主机上所运行的进程之间传递数据；
- 文件锁定：为防止其他进程读取或者更新文件内容，允许某进程对文件的部分区域加以锁定；
- 消息队列：用于在进程间交换信息（数据包）；
- 信号量：用来同步进程的动作；
- 共享内存：。。共享一块内存，然后某个进程改变了它，就要通知其它使用这块内存的进程。

# 信号

人们往往将信号称为：软件中断。进程收到信号，这就意味着某一时间或异常情况的发生。

发生下列情况之一，内核可向进程发送信号：

- 用户键入中断字符，通常是 Ctrl + C（Mac使用者表示，感触很深.. 真的，这东西很有用）；
- 进程的子进程之一已经终止；
- 有进程设定的定时器已经到期；
- 进程尝试访问无效的内存地址。

收到信号之后，进程会根据信号采取如下动作之一：

- 忽略信号；
- 被信号 杀死；
- 先挂起，之后再被专用信号唤醒。

# 线程

多线程都不会的话.. 那基本上找不到工作了。

每个进程可以执行多个线程。可将线程想象为共享同一虚拟内存及一干其它属性的进程。每个线程都会执行相同的程序代码，共享同一数据区域和堆。不同的是，每个线程都拥有自己的栈，用来装载本地变量和函数调用链接信息。

线程的优点在于协同线程之间的数据共享（**通过全局变量**）更加容易。多线程应用能从多处理器硬件的并行处理中获益匪浅。

# 进程组和shell任务控制

shell执行的每个程序都会在一个新进程内发起。

还有一种交互式特性，名为任务控制。该特性允许用户同时执行并操控多条命令或管道。

详细的说明估计在后面。慢慢来吧。

# 会话、控制终端和控制进程

会话是指一组进程组（任务）。会话中的所有进程都具有相同的会话标识符。会话首进程（session leader）是指创建会话的进程，其进程ID为会话ID。

用会话最多的，其实是shell。

所以，通常会话都与某个控制终端相关。

打开控制终端会致使会话首进程称为终端的控制进程。

# 伪终端

伪终端的话，其实就是一对相互连接的虚拟设备，又称为主从设备。在这对设备之前，设有一条IPC通道，可供数据进行双向传递。

比如。。telnet、ssh之类的。

# 日期和时间

进程主要涉及到两种类型的时间：

- 真实时间：指的是进程的生命期内（所经历的时间或时钟时间），以某个标准时间点为起点测得的时间。一般是1970年1.1的凌晨开始，按秒得到是时间戳。
- 进程时间：又叫CPU时间，指的是进程自启动以来，所占用的CPU时间总量。

# 客户端/服务端架构

这个..... 虽然很久没搞web了，但还是挺熟的吧。之后看到那一章再细述。

# 实时性

实时性的话，我的理解就是有些程序对响应时间有着非常苛刻的要求。就比如银行ATM机啥的，要是要你等个一两个小时才拿到钱，这就非常尴尬了。

虽然很多应用程序都要求对输入做出快速响应，但决定性因素却在于要在时间出发的一定时效内，保证响应的交付。

# /proc文件系统

/proc。。这个好像还真没听过。

/proc 文件系统是一种虚拟文件系统，以文件系统目录和文件形式，提供一个指向内核数据结构的接口。这为查看和改变各种系统属性开启了方便之门。

大多数情况下，只有特权级进程才能修改 /proc 目录下的文件内容。