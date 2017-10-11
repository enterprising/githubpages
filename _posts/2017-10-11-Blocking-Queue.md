---
layout: post
title: 阻塞队列的原理及实现
date: 2017-10-11 13:00
categories: Java
tags: [Java]
---

* content
{:toc} 
# 阻塞队列的定义

我的理解，阻塞队列就是用来实现线程安全的东西。一般用于生产者消费者模式啥的。总之，它提供了一种线程安全的队列访问方式。

所谓的阻塞，意思就是，当遇到出队列的请求时它会先阻塞，直到队列非空的时候再出队列；

当遇到入队列的请求时，它也会阻塞，直到队列非满的时候才入队列。

# Java中的阻塞队列

好像有七八种，然后都是实现的 BlockingQueue 接口，实现了里面的几个核心的方法：put()、take()、offer()、poll()之类的。

BlockingQueue 的实现类

> BlockingQueue 是个接口，你需要使用它的实现之一来使用BlockingQueue，java.util.concurrent包下具有以下 BlockingQueue 接口的实现类：

- ArrayBlockingQueue：ArrayBlockingQueue 是一个有界的阻塞队列，其内部实现是将对象放到一个数组里。有界也就意味着，它不能够存储无限多数量的元素。它有一个同一时间能够存储元素数量的上限。你可以在对其初始化的时候设定这个上限，但之后就无法对这个上限进行修改了(译者注：因为它是基于数组实现的，也就具有数组的特性：一旦初始化，大小就无法修改)。
- DelayQueue：DelayQueue 对元素进行持有直到一个特定的延迟到期。注入其中的元素必须实现 java.util.concurrent.Delayed 接口。
- LinkedBlockingQueue：LinkedBlockingQueue 内部以一个链式结构(链接节点)对其元素进行存储。如果需要的话，这一链式结构可以选择一个上限。如果没有定义上限，将使用 Integer.MAX_VALUE 作为上限。
- PriorityBlockingQueue：PriorityBlockingQueue 是一个无界的并发队列。它使用了和类 java.util.PriorityQueue 一样的排序规则。你无法向这个队列中插入 null 值。所有插入到 PriorityBlockingQueue 的元素必须实现 java.lang.Comparable 接口。因此该队列中元素的排序就取决于你自己的 Comparable 实现。
- SynchronousQueue：SynchronousQueue 是一个特殊的队列，它的内部同时只能够容纳单个元素。如果该队列已有一元素的话，试图向队列中插入一个新元素的线程将会阻塞，直到另一个线程将该元素从队列中抽走。同样，如果该队列为空，试图向队列中抽取一个元素的线程将会阻塞，直到另一个线程向队列中插入了一条新的元素。据此，把这个类称作一个队列显然是夸大其词了。它更多像是一个汇合点。

# 拥塞队列的核心思想

主要就是一个 ReentrantLock，和两个由它创建的 Condition：notFull、notEmpty。

然后，在take、put、enqueue、dequeue 四个方法里面对这两个信号条件进行控制。

take、put、offer、poll 这几个对队列进行操作的方法，在操作前，都必须加锁。

核心思想就是：空就不让你取，满就不让你加。

# 自己实现一个简单的阻塞队列

```java
/**
 * 阻塞队列的实现
 * Created by peng.tan on 17/10/11.
 */
public class BlockQueueComplete {
    private ReentrantLock lock;
    private Condition notFull;
    private Condition notEmpty;
    private int count;
    private int putIndex;
    private int takeIndex;
    private int[] data = null;

    BlockQueueComplete(int size) {
        data = new int[size];
        lock = new ReentrantLock();
        notEmpty = lock.newCondition();
        notFull = lock.newCondition();
    }

    /**
     * 正常的入队列
     *
     * @param t
     * @return
     */
    public boolean offer(int t) throws InterruptedException {
        // 不能插入 null，所以一般要先进行判空（空表示没有，或者说之前那个已经被弹出了）
        ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            if (count == data.length)
                return false;
            else
                enqueue(t);
            return true;
        } finally {
            lock.unlock();
        }
    }

    private void enqueue(int t) {
        data[putIndex] = t;
        if (++putIndex == data.length)
            putIndex = 0;
        count++;
        notEmpty.signal();  // 不空了  Wakes up one waiting thread.
    }

    /**
     * 正常的出队列，有则返回内容，没有就返回null(这里是返回-1)
     *
     * @return
     */
    public int poll() throws InterruptedException {
        ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            return count == 0 ? -1 : dequeue();
        } finally {
            lock.unlock();
        }
    }

    private int dequeue() {
        int t = data[takeIndex];
        data[takeIndex] = -1; //对这一项进行删除.. 嗯，本来是要赋值为null的
        if (++takeIndex == data.length)
            takeIndex = 0;
        count--;
        notFull.signal();
        return t;
    }

    /**
     * 阻塞队列的 入队列，到队列不满的时候才入
     *
     * @param t
     */
    public void put(int t) throws InterruptedException {
        // 帮 t 判空
        ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == data.length) {
                notFull.await();
            }
            enqueue(t);
        } finally {
            lock.unlock();
        }
    }

    /**
     * 阻塞队列的 出队列，当队列不空的时候才出
     *
     * @return
     * @throws InterruptedException
     */
    public int take() throws InterruptedException {
        ReentrantLock lock = this.lock;
        lock.lockInterruptibly();
        try {
            while (count == 0) {
                notEmpty.await();
            }
            return dequeue();
        } finally {
            lock.unlock();
        }
    }
}
```

说明：

1. 这里只实现了它的几个核心方法，还有一些非核心的方法比如 size()、contains()啥的没写
2. 参考的是Java源码中 ArrayBlockingQueue的代码
3. 还是源码强大

# 测试自己的代码

```java
public static void main(String[] args) throws InterruptedException {
    BlockQueueComplete blockQueueDemo = new BlockQueueComplete(10);
    blockQueueDemo.put(4);
    System.out.println(Arrays.toString(blockQueueDemo.data));
    blockQueueDemo.take();
    System.out.println(Arrays.toString(blockQueueDemo.data));
    blockQueueDemo.put(5);
    System.out.println(Arrays.toString(blockQueueDemo.data));
    blockQueueDemo.take();
    System.out.println(Arrays.toString(blockQueueDemo.data));
    blockQueueDemo.put(6);
    blockQueueDemo.put(7);
    blockQueueDemo.offer(8);
    System.out.println(Arrays.toString(blockQueueDemo.data));
    blockQueueDemo.poll();
    System.out.println(Arrays.toString(blockQueueDemo.data));
}
```

运行结果为：

> [4, 0, 0, 0, 0, 0, 0, 0, 0, 0]
>
> [-1, 0, 0, 0, 0, 0, 0, 0, 0, 0]
>
> [-1, 5, 0, 0, 0, 0, 0, 0, 0, 0]
>
> [-1, -1, 0, 0, 0, 0, 0, 0, 0, 0]
>
> [-1, -1, 6, 7, 8, 0, 0, 0, 0, 0]
>
> [-1, -1, -1, 7, 8, 0, 0, 0, 0, 0]