---
layout: post
title: Java8新玩法之时间类
date: 2017-7-6 17:00
categories: Java
tags: [Java]
---

* content
{:toc}
## 先看看老玩法

获取时间 + 处理时间 + 格式化时间

```java
Date date2 = new Date();
        long startdate = date2.getTime();
        long end = date2.getTime() - 30000;
        long s = date2.getTime() - 330000;
        Date start = new Date(s);
        SimpleDateFormat sdf = new SimpleDateFormat("%22yyyyMMdd%20HH:mm:ss%22");// localdatetime
        String startTime = sdf.format(start);
        String endTime = sdf.format(end);
```

运行结果：

> 开始时间为：%2220170706%2016:57:42%22  
> 结束时间为：%2220170706%2017:02:42%22

## Java 8新玩法 — LocalDateTime

先安利一下，这是Java 8发布的一个功能十分强大的时间类。

在Java8之前，时间和日期处理一直都被很多程序员吐槽，说太难用。比如，Date类在SQL包和Util包里面都有，鬼知道要用哪个，其次就是格式化的话又要用java.text.DateFormat类处理。而且又是日期又是时间的，会很复杂。 

在Java8中新的日期和时间库很好的解决了以前日期和时间类的很多弊端。这里据说借鉴了第三方日期库：joda 的很多优点。

Java 8 的java.time中，主要包含了：

```java
Instant：时间戳
Duration：持续时间，时间差
LocalDate：只包含日期，比如：2016-10-20
LocalTime：只包含时间，比如：23:12:10
LocalDateTime：包含日期和时间，比如：2016-10-20 23:14:21
Period：时间段
ZoneOffset：时区偏移量，比如：+8:00
ZonedDateTime：带时区的时间
Clock：时钟，比如获取目前美国纽约的时间
```

时间格式化类用的是 java.time.format 包中的：

```java
DateTimeFormatter：时间格式化
```

demo：获取时间差，当前时间和5分钟之前的时间

```java
 LocalDateTime startPoint = LocalDateTime.now();
 DateTimeFormatter dft = DateTimeFormatter.ofPattern("%22yyyyMMdd%20HH:mm:ss%22");
 String startTime = startPoint.format(dft).toString();
 LocalDateTime endPoint = startPoint.plusMinutes(5);
 String endTime = endPoint.format(dft).toString();
 System.out.println("开始时间为："+startTime);
 System.out.println("结束时间为："+endTime);
```

运行结果：

> 开始时间为：%2220170706%2017:23:21%22  
> 结束时间为：%2220170706%2017:28:21%22

真的很强大！！！不用考虑什么加多少时间会影响前面的年啊月啊啥的，都会自动处理，而且不用调用乱七八糟的类。