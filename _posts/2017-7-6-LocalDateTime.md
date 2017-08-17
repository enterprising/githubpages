---
layout: post
title: Java8-时间类
date: 2017-7-27 10:00
categories: Java
tags: [Java]
---

* content
{:toc}
# 对比

求当前时间往前的五分钟

## 先看看老玩法

获取时间 + 处理时间 + 格式化时间

```java
Date date2 = new Date();
long startdate = date2.getTime();
long end = date2.getTime();
long s = date2.getTime() - 330000;
Date start = new Date(s);
SimpleDateFormat sdf = new SimpleDateFormat("%22yyyyMMdd%20HH:mm:ss%22");// localdatetime
String startTime = sdf.format(start);
String endTime = sdf.format(end);
```

运行结果：

> 开始时间为：%2220170706%2016:57:42%22  
> 结束时间为：%2220170706%2017:02:42%22

## Java 8新玩法

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

# 求时间间隔

官方API：<https://docs.oracle.com/javase/8/docs/api/>

## 求两个日期之间间隔了多少年月日

LocalDate

> **Period** between = Period.between(d.toLocalDate(), target.toLocalDate());

第一个参数为开始时间，最后的参数为结束时间

当差300天时，输出为：

> P-9M-27D

就是9个月27天

## 求两个时间的时间差

LocalTime

> **Duration** duration = Duration.between(d,target);

第一个参数为开始时间，最后的参数为结束时间

当差300天时，输出为：

> PT-7198H-43M-28.5S

7198小时43分钟...

# 格式化时间

> DateTimeFormatter dft = DateTimeFormatter.ofPattern("%22yyyyMMdd%20HH:mm:ss%22");
>
>  String time = xxx.format(dft).toString();

说明：DateTimeFormatter.ofPattern()里面可以包含复杂的字符串，只要识别出yyyy mm dd之类的就OK，不会影响到整体。demo中这样是为了满足具体API接口要求的特殊格式化。

# 字符串转换成LocalDateTime

```java
public static void main(String[] args) {
  String x = "2017-08-11T06:24:40";
  LocalDateTime ld = LocalDateTime.parse(x);
  DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:SS");
  System.out.println(ld.format(df));
}
```

运行结果：

> 2017-08-11 06:24:00

说明：LocalDateTime.parse(字符串)，可以将目标字符串转换成LocalDataTime对象。

返回的是：//精确到秒 得到相对于1970-01-01 00:00:00 UTC的一个时间 

那么问题来了，时间戳是秒还是毫秒呢？

答案是秒。所以通过上面说的这种方法能正确的得到时间戳。

时区是Java8时间类里面的一个比较重要的点，虽然略麻烦，但是还是比较重要的。

# 时区问题

> ZoneId.of("xxxx");

xxxx是字符串，可以用： System.out.println(ZoneId.getAvailableZoneIds()); 找出自己想要的时区。

获取系统时区：

> ZoneId.systemDefault()

# LocalDateTime转时间戳

新的时间类对时区特别敏感。

> LocalDateTime r = LocalDateTime.now();
>
> System.out.println(time.atZone(ZoneId.systemDefault()).toInstant().getEpochSecond());

输出：1502436630

说明：最重要的是时间戳：atZone(xx)，里面具体的时区怎么设置，在上一小节已经说明了。

设置好时区之后，xxx.toInstant().getEpochSecond();

# long转时间戳再转LocalDateTime

例如，startTime = 1502899200，我们需要转成LocalDateTime。

> Instant instant = Instant.ofEpochSecond(startTime);
>
> LocalDateTime slocal = LocalDateTime.ofInstant(instant,ZoneId.systemDefault());

Instant是java.time包里面的时间戳。

先把long对象转换成时间戳对象，然后调用LocalDateTime.ofInstant()，这个方法的第一个参数是时间戳，第二个参数是时区。

加上格式转换，看下运行效果：

```java
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
Instant instant = Instant.ofEpochSecond(startTime);
LocalDateTime slocal = LocalDateTime.ofInstant(instant,ZoneId.systemDefault());

System.out.println(startTime);
System.out.println("在 "+ slocal.format(dateTimeFormatter)+ " ~ ");
```

运行效果：

> 1502899200
>
> 在 2017-08-17 00:00:00 ~ 

# time类详细介绍

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

安利一下，这是Java 8发布的一个功能十分强大的时间类。

在Java8之前，时间和日期处理一直都被很多程序员吐槽，说太难用。比如，Date类在SQL包和Util包里面都有，鬼知道要用哪个，其次就是格式化的话又要用java.text.DateFormat类处理。而且又是日期又是时间的，会很复杂。 

在Java8中新的日期和时间库很好的解决了以前日期和时间类的很多弊端。这里据说借鉴了第三方日期库：joda 的很多优点。

