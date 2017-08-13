---
layout: post
title: IO多路复用 select poll epoll
date: 2017-8-13 11:00
categories: Linux
tags: [Linux,计算机网络]
---

* content
{:toc} 
说实话.. 这个之前听都没听过。第一次学习，希望能有个大概的了解。

# 介绍

select,poll,epoll 都是**IO多路复用**机制。

所谓的IO多路复用就是通过一种机制，可以监听多个文件描述符FD，一旦某个FD就绪，就可以通知程序进行相应的读写操作。

但它们三个本质上都是同步IO，因为它们都需要在读写事件就绪之后自己负责读写，换句话说就是：这个读写过程是阻塞的。而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。

与多线程和多进程相比，I/O 多路复用的最大优势是系统开销小，系统不需要建立新的进程或者线程，也不必维护这些线程和进程。

<br/>

# IO多路复用介绍

常见的 **IO模式** 有四种：阻塞IO（BIO）、非阻塞IO（NIO）、异步IO（AIO）、IO多路复用。[常见IO模式](http://blog.tanpeng.net/2017/07/30/Java-BIO-NIO-AIO/#bio)

所以，其实IO多路复用就是IO模式中的其中一种。

怎么说呢，你可以把select、poll、epoll就当做IO多路复用，这三者就代表着IO多路复用。当然也是IO多路复用的实现方式。

<br/>

# IO多路复用详解

![](https://segmentfault.com/img/bVm1c5)

当用户调用了select，整个进程就会被阻塞，而 kernel (内核) 会监视所有select负责的Socket，当其中任何一个Socket中的数据准备好了，select就会返回。这时候application(用户进程)再调用read操作，将数据从内核拷贝到用户进程。

总结：

> I/O多路复用的特点就是：通过一种机制，让一个进程能等待多个文件描述符（FD），而这些文件描述符（Socket描述符）其中任意一个进入到读就绪状态，select() 函数就可以返回。



# select

> int select (int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);

select 函数监视的文件描述符分3类，分别是writefds、readfds、和exceptfds。调用后select函数会阻塞，直到有描述副就绪（有数据 可读、可写、或者有except），或者超时（timeout指定等待时间，如果立即返回设为null即可），函数返回。当select函数返回后，可以 通过遍历fdset，来找到就绪的描述符。

select目前几乎在所有的平台上支持，其良好**跨平台**支持也是它的一个优点。

select的一 个缺点在于单个进程能够监视的文件描述符的**数量存在最大限制**，在Linux上一般为1024，可以通过修改宏定义甚至重新编译内核的方式提升这一限制，但 是这样也会造成效率的降低。

调用过程：

![select](http://images.cnitblog.com/blog/305504/201308/17201205-8ac47f1f1fcd4773bd4edd947c0bb1f4.png)

select的缺点：

- 每次调用select都需要把文件描述符（FD）从用户态拷贝到内核，开销比较大
- 每次都需要在内核遍历传入的文件描述符（FD）
- select支持文件数量比较小，默认是1024。当然，也可以通过修改宏定义改掉，但这会造成效率的降低。

<br/>

# poll

poll的意思是轮训

> int poll (struct pollfd *fds, unsigned int nfds, int timeout);

poll和select本质上是一样的，只是描述fd集合的方式不同。

poll使用的是pollfd结构，select使用的是fd_set结构。

其它都差不多。不赘述。

<br/>

# epoll

epoll是对select和poll的改进，所以.. 这个肯定比上面那两个厉害。而且改正了select、poll的三个缺点和不足。

相对于select和poll来说，epoll更加灵活，**没有描述符限制**。epoll使用**一个文件描述符管理多个描述符**，将用户关系的文件描述符的事件存放到内核的一个**事件表**中，这样在用户空间和内核空间的**copy只需一次**。

三个接口：

> int epoll_create(int size)；//**创建一个epoll的句柄**，size用来告诉内核这个监听的数目一共有多大
>
> int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)；//**注册要监听的事件类型**
>
> int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout); // **等待事件的产生**

看上面的接口就知道它有多牛逼了，因为select和poll都只提供了一个方法，这里一下子就提供了三个。

简单总结一下epoll的优点：

- 每次注册新事件到epoll句柄都会**把所有的fd拷贝进来**，而不是在epoll_wait中重复拷贝，这样确保fd只会被拷贝一次
- epoll不是像select/poll那样每次都把fd加入等待队列，epoll**把每个fd指定一个回调函数**，当设备就绪时，唤醒等待队列的等待者就会调用其它的回调函数，这个回调函数会把就绪的fd放入一个就绪链表。epoll_wait就是在这个就绪链表中查看有没有就绪fd。
- epoll没有fd数目限制

但也不是啥时候都好

> 如果没有大量的idle -connection或者dead-connection，epoll的效率并不会比select/poll高很多，但是当遇到大量的idle- connection，就会发现epoll的效率大大高于select/poll。

<br/>

# 总结

（1）select，poll实现需要自己不断轮询所有fd集合，直到设备就绪，期间可能要睡眠和唤醒多次交替。而epoll其实也需要调用 epoll_wait不断轮询就绪链表，期间也可能多次睡眠和唤醒交替，但是它是设备就绪时，调用回调函数，把就绪fd放入就绪链表中，并唤醒在 epoll_wait中进入睡眠的进程。虽然都要睡眠和交替，但是select和poll在“醒着”的时候要遍历整个fd集合，而epoll在“醒着”的 时候只要判断一下就绪链表是否为空就行了，这节省了大量的CPU时间。这就是回调机制带来的性能提升。

（2）select，poll每次调用都要把fd集合从用户态往内核态拷贝一次，并且要把current往设备等待队列中挂一次，而epoll只要 一次拷贝，而且把current往等待队列上挂也只挂一次（在epoll_wait的开始，注意这里的等待队列并不是设备等待队列，只是一个epoll内 部定义的等待队列）。这也能节省不少的开销。

<br/>

# 参考资料

1、《select、poll、epoll之间的区别总结》。shy、gril 2016.5。<https://yq.aliyun.com/articles/48668>

2、《 [Linux系统编程——I/O多路复用select、poll、epoll的区别使用](http://blog.csdn.net/tennysonsky/article/details/45745887)》。2015.5

3、《[select、poll、epoll之间的区别总结[整理\]](http://www.cnblogs.com/Anker/p/3265058.html)》。Anker。2013.8。<http://www.cnblogs.com/Anker/p/3265058.html>

4、《[Linux IO模式及 select、poll、epoll详解](https://segmentfault.com/a/1190000003063859)》