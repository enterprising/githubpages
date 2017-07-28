---
layout: post
title: Java 任务调度
date: 2017-7-27 10:00
categories: Java
tags: [Java]
top: true
---

* content
{:toc}
# Timer

最简单的一种实现任务调度的方法

## Timer介绍

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

## demo-two

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

## Timer的缺陷

- Timer对调度的支持是基于绝对时间的，而不是相对时间。所以对系统时间的改变非常敏感。
- Timer线程是**无法捕获异常**的。如果TimerTask抛出RuntimeException，Timer会终止所有任务的运行。
- 如果完成某个计时器任务的时间太长，那么它会“独占”计时器的任务执行线程，从而可能**延迟后续任务的执行。**

解决思路：用**ScheduledExecutor**替代Timer

## Timer总结

since Java1.3 老古董了，早就不用了。老大说千万不要用。

现在用啥？Java1.5出了一个 **ScheduledExecutor**

<br/>

# ScheduledExecutor

since 1.5

基于线程池设计。设计思想是：**每一个被调度的任务都会由线程池中的一个线程去执行**，因此任务是**并发**（concurrent）执行的，相互之间不会受到干扰。

需要注意的是，只有当任务的执行时间到来时，ScheduledExecutor才会真正启动一个线程，其余时间ScheduleedExecutor都是在轮询任务的状态。

## demo

```java
public class ScheduledExecutorTest implements Runnable {
    private String jobName = "";

    public ScheduledExecutorTest(String jobName) {
        super();
        this.jobName = jobName;
    }

    @Override
    public void run() {
        System.out.println("execute " + jobName);
    }

    public static void main(String[] args) {
        ScheduledExecutorService service = Executors.newScheduledThreadPool(10);

        long initialDelay1 = 1;
        long period1 = 1;
        // 从现在开始。每间隔1秒钟执行一次job1
        service.scheduleAtFixedRate(new ScheduledExecutorTest("job1"), initialDelay1, period1, TimeUnit.SECONDS);

        long initialDelay2 = 1;
        long delay2 = 2;
        // 从现在开始。每间隔2秒钟执行一次job2
        service.scheduleAtFixedRate(new ScheduledExecutorTest("job2"), initialDelay2, delay2, TimeUnit.SECONDS);

    }

}
```

运行效果：

> execute job1  
> execute job2  
> execute job1  
> execute job1  
> execute job2  
> execute job1  
> execute job1  
> execute job2  
> execute job1  
> execute job1  
> execute job2  
> execute job1  
> execute job1  
> execute job2  

job1每隔一秒执行一次，job2每隔两秒执行一次。

## 详解

1、实例化一个ScheduledExecutorService对象，后面括号里面的参数代表的是线程池里面的线程数

ScheduledExecutorService service = Executors.newScheduledThreadPool(10);

2、任务调度。第一参数是可以执行的任务，第二个参数代表多久之后执行，第三个参数代表每隔多久执行一次，第四个参数是前两个参数的单位。

service.scheduleAtFixedRate(new ScheduledExecutorTest("job1"), initialDelay1, period1, TimeUnit.SECONDS);

> 官方API说明：
>
> scheduleAtFixedRate**(Runnable command, long initialDelay, long period, TimeUnit unit)** 
> Creates and executes a periodic action that becomes enabled first after the given initial delay, and subsequently with the given period; that is executions will commence after initialDelay then initialDelay+period, then initialDelay + 2 * period, and so on.

更加详细的介绍：<https://docs.oracle.com/javase/1.5.0/docs/api/>

官方的文档，永远都是最好的。

<br/>

# ScheduledExecutor复杂应用

Timer 和 ScheduledExecutor 都仅能提供基于开始时间与重复间隔的任务调度，不能胜任更加复杂的调度需求。比如，**设置每天的 11：00：00 执行任务**。该功能使用 Timer 和 ScheduledExecutor 都不能直接实现，

但我们可以借助 **Calendar** 间接实现该功能。

Calendar？？？？excuse me？？

查了一下Calendar的源码

> * @author Mark Davis, David Goldsmith, Chen-Lieh Huang, Alan Liu  
> * @since JDK1.1

作为一名紧跟时代发展的21世纪青少年，当然不会用了。

要用当然就要用目前（2017年）为止最新的技术了，这里我使用的是Java8 time包里面的LocalDataTime等相关工具类。

关于time包的介绍可以看我另外一篇博客：《Java8-时间类》：<http://blog.tanpeng.net/2017/07/27/LocalDateTime/>

## 获取时间

```java
LocalDateTime d = LocalDateTime.now();
LocalDateTime target = LocalDateTime.of(d.getYear(),d.getMonth(),d.getDayOfMonth(),11,00,00,00);
target = target.plusDays(1);
Duration duration = Duration.between(d,target);
System.out.println(duration.toMillis());
```

获取当前时间距离明天上午 11:00:00的毫秒数

## 任务调度

```java
//计算从当前时间到最近一次执行时间的时间间隔
long delay = duration.toMillis();
//计算执行周期为一天
long period = 24 * 60 * 60 * 1000;

ScheduledExecutorService service = Executors.newScheduledThreadPool(10);
//从现在开始delay毫秒之后，每隔一星期执行一次job1
service.scheduleAtFixedRate(test, delay, period, TimeUnit.MILLISECONDS);
```

说明：duration就是上面求出来的一个Duration对象（时间间隔），然后toMillis()代表获取它的毫秒数。

## 完整的代码

```java
public class ScheduledExceutorTest2 implements Runnable {
    private String jobName = "";
    private Period between;
    public ScheduledExceutorTest2(String jobName) {
        super();
        this.jobName = jobName;
    }
    @Override
    public void run() {
        System.out.println("Date = " + new Date() + ", execute " + jobName);
    }
    public static void main(String[] args) throws Exception {
        ScheduledExceutorTest2 test = new ScheduledExceutorTest2("job1");
        LocalDateTime d = LocalDateTime.now();
        LocalDateTime target = LocalDateTime.of(d.getYear(), d.getMonth(), d.getDayOfMonth(), 11, 00, 00, 00);
        target = target.plusDays(1);
        Duration duration = Duration.between(d, target);
        //计算从当前时间到最近一次执行时间的时间间隔
        long delay = duration.toMillis();
        //计算执行周期为一天
        long period = 24 * 60 * 60 * 1000;
        ScheduledExecutorService service = Executors.newScheduledThreadPool(10);
        //从现在开始delay毫秒之后，每隔一星期执行一次job1
        service.scheduleAtFixedRate(test, delay, period, TimeUnit.MILLISECONDS);
    }
}
```

<br/>

# Quartz

这个我是一定要学的，不过暂时没时间。先挖个坑，有时间再填。

<br/>

# 其它任务调度工具推荐

java.util.Timer 简单易用，比较适合提供轻量级的计时器功能。由于其创建的线程会超出容器的管理范围，因此不能应用于管理的环境中。

适合大型项目下的计时器：

Commonj Timer ：IBM和BEA研发的一款计时器

WebSphere Application Server Scheduler ：IBM研发的计时器

<br/>

# 参考资料

1、几种任务调度的 Java 实现方法与比较  <https://www.ibm.com/developerworks/cn/java/j-lo-taskschedule/index.html> 2011.9.10

2、多种计时器的比较与分析 <https://www.ibm.com/developerworks/cn/java/j-lo-timer/>  2008.5.26

3、java定时器Timer <http://www.jianshu.com/p/522586988368> 2017.6.8