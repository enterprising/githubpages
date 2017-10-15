---
layout: post
title: 阻塞队列-数组实现
date: 2017-10-15 17:00
categories: Java
tags: [Java]
---

* content
{:toc} 
# 阻塞队列的介绍

阻塞队列和一般的队列，最本质上的区别在于，它在取元素和放元素之前多了一步判断的操作。

在取的时候，它会先判断队列里面是否为空。如果空了，那么就等到非空的时候再取；

在放的时候，它会先判断队列里面是否满了。如果满了，那么久等到没满的时候再放。

本文主要参考了，JDK源码中 ArrayBlockingQueue 的实现。

里面主要是通过一个可重入锁 ReentrantLock ，以及由它所创造的两个Condition：notEmpty、notFull，来实现上述效果的。

# 关于ReentrantLock的介绍

这个是JDK5.0开始的，主要实现了Lock接口（也是5.0开始有的）。

它比synchronized要强大。synchronized又叫内置锁，你只要用个synchronized关键字包一下就OK，中间就是线程安全的。但这里有个坏处就是，你没办法中断它，如果一个线程在等锁，你想去打扰它 是不可能的。

ReentrantLock是显式锁，所谓的显式，意思是你用 lock() 方法加锁之后，必须手动地用 unlock()方法释放锁。

它厉害的地方主要在于，线程是可中断的。你可以中断任何一个正在等待获取锁的线程。

使用demo：

```java
ReentrantLock lock = this.lock;
lock.lockInterruptibly();
try {
	// do sth
} finally {
  lock.unlock();
}
```

# 关于阻塞队列里面的锁

## take时

先锁起来，然后判断队列中元素的个数是不是为0，为0，那么等notEmpty这个Condition被触发。

直到notEmpty被触发了，才开始取。

最后更新数目，更新取的下标。

最后释放锁。

## put时

先锁起来，然后判断队列中元素的个数是不是为设置的容量大小（就是是不是满了的意思），如果是，那么等notFull这个Condition被触发。

直到notFull被触发了，才开始放。

最后更新数目，更新放的下标。

最后释放锁。

# 代码

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

