---
layout: post
title: 阻塞队列-链表实现
date: 2017-10-15 17:10
categories: Java
tags: [Java]
---

* content
{:toc} 
# 阻塞队列的介绍

阻塞队列和一般的队列，最本质上的区别在于，它在取元素和放元素之前多了一步判断的操作。

在取的时候，它会先判断队列里面是否为空。如果空了，那么就等到非空的时候再取；

在放的时候，它会先判断队列里面是否满了。如果满了，那么久等到没满的时候再放。

本文主要参考了，JDK源码中 LinkedBlockingQueue 的实现。

# LinkedBlockingQueue的介绍

LinkedBlockingQueue里面有两个锁，一个控制take，一个控制put。

takeLock，会创建一个Condition：notEmpty，主要用于取的时候做判空操作；

putLock，会创建一个Condition：notFull，主要用于放的时候做判满操作；

里面内部维护了一个链表，其中链表的节点类也是需要自己写的。

在入队和出队列的时候，要主要链表的操作，还有count的原子性。

# 代码

```java
/**
 * Created by peng.tan on 17/10/13.
 */
public class LinkedBlockingQueueDemo {
    static class Node {
        int item;
        Node next;

        Node(int x) {
            item = x;
        }
    }
    private ReentrantLock takeLock = new ReentrantLock();
    private Condition notEmpty = takeLock.newCondition();

    private ReentrantLock putLock = new ReentrantLock();
    private Condition notFull = putLock.newCondition();

    private Node head;
    private Node last;

    private int size; //容量
    private AtomicInteger count = new AtomicInteger(); //当前数量

    LinkedBlockingQueueDemo(int size) {
        this.size = size;
        head = new Node(-1);
        last = head;
    }

    public void put(int x) throws InterruptedException {
        ReentrantLock putLock = this.putLock;
        AtomicInteger count = this.count;
        putLock.lockInterruptibly();
        int c;
        try {
            while (count.get() == size) {
                notFull.await();
            }
            // 入队列
            last.next = last = new Node(x);
            c = count.getAndIncrement();
            if (c + 1 < size)
                notFull.signal();
        } finally {
            putLock.unlock();
        }
    }

    public int take() throws InterruptedException {
        int result;
        int c; //表示当前容量
        ReentrantLock takeLock = this.takeLock;
        AtomicInteger count = this.count;
        takeLock.lockInterruptibly();
        try {
            while (count.get() == 0) {
                notEmpty.await();
            }
            // 出队列，这个有点麻烦
            Node first = head.next;
            result = first.item;
            head.next = head; // 方便回收
            first.item = -1; //表示删除这个点的值
            head = first;

            c = count.getAndDecrement();
            if (c - 1 > 0)
                notEmpty.signal();

        } finally {
            takeLock.unlock();
        }
        return result;
    }
}
```

