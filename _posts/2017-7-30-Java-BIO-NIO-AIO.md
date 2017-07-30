---
layout: post
title: Java BIO、NIO、AIO
date: 2017-7-30 1:00
categories: Java
tags: [Java,多线程,计算机网络,NIO]
---

* content
{:toc}
# 写在前面

对于Java里面同步异步阻塞非阻塞那些，其实我的理解都只停留在最基础的层面，有一些概念可能还非常模糊。最近由于工作需要准备研究一下netty，查了一下发现-Netty 是一个基于 JAVA NIO 类库的异步通信框架。啥是NIO.. 一脸懵逼，看来不先搞懂这个是没办法进行下去了。看到NIO顺便又了解了一下BIO和AIO，所以就有了这篇文章。

<br/>

# 同步和异步

不能光从字面意思了解，要从分布式的角度思考。

同步（synchronize）、异步（asychronize）关注的是**消息通信机制**。

所谓同步，就是在发出一个调用时，在没有得到结果之前，该调用就不返回。但是一旦调用返回，就得到返回值了。简而言之，就是**由调用者主动等待这个调用结果。** 通常所说的线程安全基本上就是指是同步的。

**使用同步IO时，Java自己处理IO的读写**

所谓异步，跟同步相反。调用在翻出之后，这个调用就直接返回了，所以没有返回结果。换句话说，就是当一个异步进程调用发出之后，调用者不会立刻得到结果。而是在调用发出之后，被调用者通过状态、通知来通知调用者，或者通过回调函数来处理这个调用。

**使用异步IO时，Java将IO读写委托给OS处理**，需要将数据缓冲区地址和大小传给OS，OS需要支持异步IO操作。

举个例子：

> 你打电话问东门酸奶店老板有没有蓝莓味的炒酸奶，如果是同步通信机制，那么老板会说：“我先看一下冰箱啊，等我确认之后（可能是5分钟也可能半小时）告诉你结果（返回结果）  **不挂电话**
>
> 而异步通信机制，书店老板直接告诉你我先确认一下，确认好了再打电话给你，然后直接把电话给挂了（不返回结果）。等确认好了，他会主动打电话给你。这里老板通过”回电“这种方式来回调。  **挂电话**

<br/>

# 阻塞和非阻塞

阻塞和非阻塞关注的是**程序在等待调用结果（消息，返回值）时的状态**。

**阻塞调用是指 调用结果返回之前，当前线程会被挂起**。调用线程只有在得到结果之后才会返回。线程挂起，就不会给这个线程分配CPU，节约了CPU资源。但是挂起线程的优先级高于其它，所以还是阻塞的。

举个例子：ATM机排队取款，你只能等待（使用阻塞IO的时候，Java调用会一直阻塞到读写完成才返回。）

**非阻塞调用是指 在不能立刻得到结果之前，该调用不会阻塞当前线程。**

还是那个例子：柜台取款，取个号，然后坐在椅子上做其他事，等广播通知，没到你的号你就不能去，但你可以不断的问大堂经理排到了没有。（使用非阻塞IO时，如果不能读写Java调用会马上返回，当IO事件分发器会通知可读写时再继续进行读写，不断循环直到读写完成）

买书的例子：

> 你打电话问书店老板有没有《分布式系统》这本书，你如果是阻塞式调用，你会一直把自己“挂起”，直到得到这本书有没有的结果.
>
> 如果是非阻塞式调用，你不管老板有没有告诉你，你自己先一边去玩了， 当然你也要偶尔过几分钟check一下老板有没有返回结果。

<br/>

# 神级例子

一看就懂。来自网络- -原作者不明

> 老张爱喝茶，废话不说，煮开水。
> 出场人物：老张，水壶两把（普通水壶，简称水壶；会响的水壶，简称响水壶）。
>
> 1 老张把水壶放到火上，立等水开。（**同步阻塞**）  
> 老张觉得自己有点傻
>
> 2 老张把水壶放到火上，去客厅看电视，时不时去厨房看看水开没有。（**同步非阻塞**）
>
> 老张还是觉得自己有点傻，于是变高端了，买了把会响笛的那种水壶。水开之后，能大声发出嘀~~~~的噪音。
>
> 3 老张把响水壶放到火上，立等水开。（**异步阻塞**）
>
> 老张觉得这样傻等意义不大
>
> 4 老张把响水壶放到火上，去客厅看电视，水壶响之前不再去看它了，响了再去拿壶。（**异步非阻塞**）
>
> 老张觉得自己聪明了。

关于同步异步、阻塞非阻塞：

> 所谓同步异步，只是对于水壶而言。
> 普通水壶，同步；响水壶，异步。
> 虽然都能干活，但响水壶可以在自己完工之后，提示老张水开了。这是普通水壶所不能及的。
> 同步只能让调用者去轮询自己（情况2中），造成老张效率的低下。
>
> 所谓阻塞非阻塞，仅仅对于老张而言。
> 立等的老张，阻塞；看电视的老张，非阻塞。
> 情况1和情况3中老张就是阻塞的，媳妇喊他都不知道。虽然3中响水壶是异步的，可对于立等的老张没有太大的意义。
>
> 所以一般**异步是配合非阻塞使用的**，这样才能发挥异步的效用。

<br/>

# BIO

同步阻塞IO。B代表blocking

服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的开销。

适用场景：Java1.4之前唯一的选择，简单易用但资源开销太高。

<br/>

# NIO

同步非阻塞IO。 N代表non-blocking 

服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有IO请求时才启动一个线程进行处理。

适用场景：连接数目多且连接比较短（轻操作）的架构，比如聊天服务器。编程较为复杂。**Java1.4**开始支持。

<br/>

# AIO

异步非阻塞IO。A代表asynchronize

服务器实现模式为一个有效请求一个线程，客户端的IO请求都是由OS先完成了再通知服务器应用去启动线程进行处理。

使用场景：连接数目多且连接比较长（重操作）的架构，比如相册服务器。重返调用了OS参与并发操作，编程比较复杂。**Java7**开始支持。

<br/>

# 参考资料

1、《Java BIO、NIO、AIO 学习》。2013.8。<http://stevex.blog.51cto.com/4300375/1284437/>

2、《Java NIO浅析》。2016.11。<https://tech.meituan.com/nio.html>

3、《怎样理解阻塞非阻塞与同步异步的区别》。2014.4。<https://www.zhihu.com/question/19732473>

4、《同步与异步、阻塞与非阻塞》。2014.8。<http://www.cnblogs.com/albert1017/p/3914149.html>