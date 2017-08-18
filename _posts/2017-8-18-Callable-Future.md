---
layout: post
title: 可以返回内容的线程
date: 2017-8-18 16:00
categories: Java
tags: [Java,多线程]
---

* content
{:toc} 
写在前面：

最近项目里面有这种需求，然后去查了一下。

说实话.. 第一次看到这种写法，我是比较嫌弃的，以为又是过时的方法。但是昨晚翻了一下《Java并发编程实战》，发现它里面居然也介绍了这个。好吧，看来还没过时。虽然现在已经熟练使用了，但为了方便日后查阅，所以整理一篇博客。

# 关于线程多线程的介绍

安利我之前写的一篇：[《线程、进程、多线程、守护线程、线程池》](http://blog.tanpeng.net/2017/08/12/thread/)    



#demo代码

线程代码：MyCallable

```java
import java.util.concurrent.Callable;

/**
 * Created by peng.tan on 17/8/18.
 */
public class MyCallable implements Callable {
    @Override
    public Object call() throws Exception {
        return "hello world";
    }
}
```

concurrent包叫做并发包，是Java5最大的亮点。Callable，就是可以返回值的线程接口了。

使用类：

```java
/**
 * Created by peng.tan on 17/8/18.
 */
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Callable myCallable = new MyCallable();
//        Thread thread = new Thread(myCallable);  // 这样是错误的
        ExecutorService pool = Executors.newFixedThreadPool(1);
        Future<String> future = pool.submit(myCallable);
        String s = future.get();
        System.out.println(s);
    }
}
```

直接用Thread的话，是不可行的。

简单的总结：Callable和Future，一个产生结果，一个拿到结果。

运行结果：

> hello world

# 详细说明

## Callable

Callable接口类似于Runnable，

但Runnable不会返回结果，并且无法抛出返回结果的异常。

Callable功能更强大一些，可以返回值，这个返回值可以被Future拿到。也就是说，Future可以拿到异步执行任务的返回值。

## Future

其实可以用FutureTask.. 但我觉得线程池更厉害一些。

Future存在的价值是为了拿到Callable的返回值。

> ExecutorService pool = Executors.newFixedThreadPool(1);
> Future\<String\> future = pool.submit(myCallable);

通过ExecutorService的submit方法执行Callable，并返回Future。

Future使用泛型，然后就能美滋滋地使拿到并使用Callable的返回值了。

# 参考资料

1、《Java并发编程实战》

2、《[JAVA多线程解惑之多线程返回值](http://tangmingjie2009.iteye.com/blog/1993251)》

3、<http://blog.tanpeng.net/2017/08/12/thread/>

4、[Java线程(七)：Callable和Future](http://blog.csdn.net/ghsau/article/details/7451464)