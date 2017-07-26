---
layout: post
title: Java定时器
date: 2017-7-26 14:00
categories: Java
tags: [Java]
---

# Timer介绍

在Java中的一个完整的定时任务由**Timer**和**TimerTask**两个类配合完成。

Timer是一种定时器工具，用来在后台线程计划执行指定任务。

TimerTask是一个抽象类，它的子类代表一个可以被Timer执行的任务。

Timer计时器可以**定时**（指定时间执行任务）、**延迟**（延迟5秒执行任务）、**周期性地执行任务**（每隔个1秒执行任务）

**定时器的实现原理就是新开一个子线程执行定时任务。**

## demo-one

```java
/**
 * Created by peng.tan on 17/7/26.
 * <p>
 * 测试java的定时任务
 */
public class TimerTest {
    Timer timer;
    public TimerTest(int time) {
        timer = new Timer();
        timer.schedule(new TimerTaskTest(), time);
    }
    public static void main(String[] args) {
        System.out.println("Timer begin....");
        new TimerTest(5000);
    }
}
class TimerTaskTest extends TimerTask {
    public void run() {
        System.out.println("Time's up!!!!");
    }
}
```

运行结果：

> Timer begin….  
> Time's up!!!!

两个输出之间间隔了5秒钟。

补充说明：

1、我们可以通过Timer(true)创建Timer对象设置子线程为守护线程，在任务执行完毕子线程立即退出。

2、关于schedule方法。可以有两个参数也可以有三个参数，但是第一个参数都是``TimerTask task`` ，demo中第二个是执行时间。

当传入三个参数的时候：

> * @param task   task to be scheduled.
> * @param firstTime First time at which task is to be executed.
> * @param **period**  time in milliseconds between successive task executions. (在两次成功的任务之间的间隔时间（单位是毫秒）)

# demo-two

无限次执行

```java
public static void main(String[] args) {
    new Timer().schedule(new TimerTask() {
        @Override
        public void run() {
            System.out.println("当前时间：" + new Date());
        }
    }, new Date(), 3000);

    // 默认从当前时间开始，以 period 间隔时间无限次数执行某一任务
    // new Timer().schedule(TimerTask task, long period);

    // 以当前时间为参考时间，在此基础上延迟 delay 毫秒数，再以 period 间隔时间无限次数执行某一任务
    // new Timer().schedule(TimerTask task, long delay, long period);
}
```

运行效果：

>当前时间：Wed Jul 26 14:40:47 CST 2017  
>当前时间：Wed Jul 26 14:40:50 CST 2017  
>当前时间：Wed Jul 26 14:40:53 CST 2017  
>当前时间：Wed Jul 26 14:40:56 CST 2017  
>当前时间：Wed Jul 26 14:40:59 CST 2017  

隔三秒打印一下当前时间

# Timer的缺陷

- Timer对调度的支持是基于绝对时间的，而不是相对时间。所以对系统时间的改变非常敏感。
- Timer线程是无法捕获异常的。如果TimerTask抛出RuntimeException，Timer会终止所有任务的运行。
- 如果完成某个计时器任务的时间太长，那么它会“独占”计时器的任务执行线程，从而可能延迟后续任务的执行。

解决思路：用ScheduledExecutorService替代Timer

## 其它计时器推荐

java.util.Timer 简单易用，比较适合提供轻量级的计时器功能。由于其创建的线程会超出容器的管理范围，因此不能应用于管理的环境中。

适合大型项目下的计时器：

Commonj Timer ：IBM和BEA研发的一款计时器

WebSphere Application Server Scheduler ：IBM研发的计时器