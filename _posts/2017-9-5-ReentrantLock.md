---
layout: post
title: Java读写锁-ReentrantReadWriteLock
date: 2017-9-5 20:00
categories: 并发
tags: [并发,Java]
---

* content
{:toc} 
# 介绍

说到锁，最熟悉的应该是synchronized和volatile，但除了这个两个Java 5还新增了一种高级的机制：ReentrantLock。前面那两种都属于内置锁，这个东西叫做显式锁。

与内置加锁机制不同，Lock提供了一种无条件的、可轮询的、定时的以及可中断的锁获取操作。所有的加锁和解锁的方法都是显式的。

它比较危险，所以不可能完全代替synchronized。当程序的执行控制离开被保护的代码块时，不会自动清除锁。

# 代码

这里演示的是读写锁。其它类似，要用的时候查API。

```java
/**
 * 用 ReentrantReadWriteLock （文件读写锁）来说实现对文件的控制
 * <p>
 * Created by peng.tan on 17/9/5.
 */
public class ReadWriteUtil {

    // 创建一个读写锁
    private static ReadWriteLock uniqueInstance = null;

    /**
     * 懒汉式双重加锁，获取读写锁 （单例模式）
     *
     * @return
     */
    public static synchronized ReadWriteLock getUniqueInstance() {
        if (uniqueInstance == null) {
            synchronized (ReadWriteUtil.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new ReentrantReadWriteLock();
                }
            }
        }
        return uniqueInstance;
    }


    /**
     * 读锁，可以很多个线程一起读
     *
     * @param path        路径
     * @param environment 环境
     * @param map         机器列表
     * @throws IOException
     */
    public static String read(Path path, Map<Integer, List<String>> map, String environment) throws IOException {
        ReadWriteLock readWriteLock = getUniqueInstance();
        StringBuilder sb = new StringBuilder();
        try {
            // 上读锁
            readWriteLock.readLock().lock();
            System.out.println(Thread.currentThread().getName() + "正在读数据...");
            Thread.sleep((long) (Math.random() * 0));
            if (Files.readAllLines(path).size() > 0) {
                String[] result = Files.readAllLines(path).get(0).split("=");
                sb.append("在 " + environment + " 环境下，峰值为：\n");
                sb.append(result[2] + " " + map.get(Integer.parseInt(result[0])) + " 负载：" + result[1] + " %\n\n");
                System.out.println("在 " + environment + " 环境下，峰值为：");
                System.out.println(result[2] + " " + map.get(Integer.parseInt(result[0])) + " 负载：" + result[1] + " %");
            } else {
                System.out.println("文件内容为空");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
        return sb.toString();
    }


    /**
     * 写锁，一次只允许一个线程持有写锁
     *
     * @param path   路径
     * @param result 要写入的数据
     */
    public static void write(Path path, String result) throws IOException, InterruptedException {
        ReadWriteLock readWriteLock = getUniqueInstance();
        try {
            //上写锁
            readWriteLock.writeLock().lock();
            Thread.sleep((long) (Math.random() * 0));
            System.out.println(Thread.currentThread().getName() + "正在写入数据..");
            Files.write(path, result.getBytes(), StandardOpenOption.TRUNCATE_EXISTING);
        } finally {
            readWriteLock.writeLock().unlock();
        }

    }

}
```

想说的东西全在代码里了，而且上面这段代码中还用到了一种非常牛逼的设计模式 - - 单例模式。

关于那个单例模式的实现，主要是用的懒汉式双重加锁。这种实现是线程安全的。