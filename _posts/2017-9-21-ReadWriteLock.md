---
layout: post
title: 关于Java读写锁的一点补充
date: 2017-9-21 15:00
categories: Java
tags: [Java]
---

之前写过一篇博客：[Java读写锁-ReentrantReadWriteLock](http://blog.tanpeng.net/2017/09/05/ReentrantLock/)

主要就是介绍了一下读写锁。

但经过一段时间的运行发现有点点不对，再加上最近在看《Java编程思想》里面并发那一章，最后发现当时的使用姿势有点不正确。

> 要控制对共享资源的访问，首先需要把它包装进一个对象。然后把所有要访问这个资源的方法全都定义成synchronized。
>
> ​						— 《java编程思想》

之前的单例模式是用在读写锁上面，但其实需要用在整个对象上。这样的话，在外面使用的都是同一个对象。

正确的使用：

```java
public class ReadWriteUtil {
    private static final ReadWriteLock lock = new ReentrantReadWriteLock();
    private static final Lock readLock = lock.readLock();
    private static final Lock writeLock = lock.writeLock();

    public ReadWriteUtil() {
    }

    private static ReadWriteUtil uniqueReadWriteUtil = null;

    public static synchronized ReadWriteUtil getInstance() {
        if (uniqueReadWriteUtil == null) {
            synchronized (ReadWriteUtil.class) {
                if (uniqueReadWriteUtil == null) {
                    uniqueReadWriteUtil = new ReadWriteUtil();
                }
            }
        }
        return uniqueReadWriteUtil;
    }


//    // 创建一个读写锁 （错误的思路）
//    private static ReadWriteLock uniqueInstance = null;
//
//    /**
//     * 懒汉式双重加锁，获取读写锁 （单例模式）
//     *
//     * @return
//     */
//    public static synchronized ReadWriteLock getUniqueInstance() {
//        if (uniqueInstance == null) {
//            synchronized (ReadWriteUtil.class) {
//                if (uniqueInstance == null) {
//                    uniqueInstance = new ReentrantReadWriteLock();
//                }
//            }
//        }
//        return uniqueInstance;
//    }


    /**
     * 读锁，可以很多个线程一起读
     *
     * @param path        路径
     * @param environment 环境
     * @param map         机器列表
     * @throws IOException
     */
    public String read(Path path, Map<Integer, List<String>> map, String environment) throws IOException {
        StringBuilder sb = new StringBuilder();
        try {
            // 上读锁
            readLock.lock();
//            System.out.println(Thread.currentThread().getName() + "正在读数据...");
            if (Files.readAllLines(path).size() > 0) {
                String[] result = Files.readAllLines(path).get(0).split("=");
                sb.append("在 " + environment + " 环境下，峰值为：\n");
                sb.append(result[2] + " " + map.get(Integer.parseInt(result[0])) + " 负载：" + result[1] + " %\n");
            } else {
                System.out.println("文件内容为空");
            }
        } finally {
            readLock.unlock();
        }
        return sb.toString();
    }


    /**
     * 写锁，一次只允许一个线程持有写锁
     *
     * @param path   路径
     * @param result 要写入的数据
     */
    public void write(Path path, String result) throws IOException, InterruptedException {
        try {
            //上写锁
            writeLock.lock();
//            System.out.println(Thread.currentThread().getName() + "正在写入数据..");
            Files.write(path, result.getBytes(), StandardOpenOption.TRUNCATE_EXISTING);
        } finally {
            writeLock.unlock();
        }
    }


    /**
     * 读锁，可以很多个线程一起读
     *
     * @param path 路径
     * @throws IOException
     */
    public String readWithNotHandle(Path path) throws IOException {
        String result = "";
        try {
            // 上读锁
            readLock.lock();
//            System.out.println(Thread.currentThread().getName() + "正在读数据...");
            if (Files.readAllLines(path).size() > 0) {
                result = Files.readAllLines(path).get(0);
            } else {
                System.out.println("文件内容为空");
            }
        } finally {
            readLock.unlock();
        }
        return result;
    }

}
```