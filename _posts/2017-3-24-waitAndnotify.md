---
layout: post
title: 关于wait()方法和notify()方法的一个典型例题
date: 2017-03-24 15:40
categories: 操作系统
tags: [多线程,操作系统]
---
![enter description here][1]  
会输出：  
Thread 2 sent notify.  
Thread 1 wake up  

原因：  
这里主要涉及到的问题是**锁**。  
wait()方法会释放掉锁，然后需要notify()或者notifyAll()来唤醒。但是有个地方必须要注意。 notify()调用之后并不是马上就释放掉对象锁的。  
t1 启动后执行 obj.wait() 时，进入阻塞状态，让出时间片并释放锁，等待其他线程的唤醒。然后 t2 获取到 obj，并唤醒 t1，待 t2 执行完毕，释放锁后，t1 再继续执行。  

综上，答案是  
> Thread 2 sent notify.  
Thread 1 wake up  

  [1]: http://omphwvjh0.bkt.clouddn.com/1490764796231.jpg 