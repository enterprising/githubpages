---
layout: post
title: JSON解析库性能比较
date: 2017-7-14 10:00
categories: Java
tags: [Java] 
---

目前非常火的几种解析库：**fastjson、json-lib、json-simple、jackson**

### 1、本地常规测试

将999条日志循环处理100次，既99900条日志，各处理5次，将循环放在try-catch块内，获得如下结果：

| 单文：毫秒       | 最小值   | 最大值   | 平均值     |
| ----------- | ----- | ----- | ------- |
| fastjson    | 14527 | 15425 | 14807.2 |
| json-lib    | 38741 | 40157 | 39502.8 |
| json-simple | 21580 | 22073 | 21828.6 |
| jackson     | 12684 | 13577 | 12974   |

### 2、Storm集群Topology测试

将上述逻辑分别放置在4个不同的bolt中，消费同一个spout下发的pubt日志，所有compnent并发数为1，worker数=executor数，保证独占一个jvm。

处理日志的耗时，既execute-latency，四个bolt对比如下：

![image2016-6-14 17-15-31](https://ws3.sinaimg.cn/large/006tKfTcly1fhjlan7ld6j30xj06jgmm.jpg)

与单机的测试结果一致，其中jackson与fastjson最快，net.sf的json-lib性能远远不及其他三种库。

四个bolt所在的worker的jvm的ParNew情况如下：

![image2016-6-14 18-58-41](https://ws4.sinaimg.cn/large/006tKfTcly1fhjlbd4vdmj30xu0e1q5p.jpg)

可以看到YGC的耗时和次数，均是jackson最优，json-lib最差。

同时，随着topology的持续运行，进入业务高峰期后，由于capacity变高，json-lib和simple-json所在的bolt不断发生worker重启现象，更换slot，导致监控不连贯。

### 3、总结

jackson与fastjson最快，net.sf的json-lib性能远远不及其他三种库。

在业务高峰期的时候，经过测试，最稳定的也是 jackson和fastjson

### 4、关于fastjson的简单介绍

来自阿里巴巴

GitHub链接：<https://github.com/alibaba/fastjson>

使用说明：<https://github.com/alibaba/fastjson/wiki/Samples-DataBind>

能非常方便的将JSON数据转换成Java的类和对象，非常好理解，而且方便使用。重点是还非常稳定，非常快。