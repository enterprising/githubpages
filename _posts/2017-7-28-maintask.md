---
layout: post
title: 实习任务小结
date: 2017-7-28 20:00
categories: DAL
tags: [DAL,Java]
---

* content
{:toc}
# 程序的目的

​	**监控所有的DAL机器，当发现有机器异常的时候，给出合理的解决方案。**

<br/>

# 数据结构

## 机器（host）


name、load、rate、machineGroupid

## 业务（org）

orgName、load

## 业务下面的组（Group）

groupName、load、orgName

<br/>

# 数据来源

## 本地配置文件

wg的 dal-proxy-ports.cfg、go proxy-front-port.cfg；xg的 dal-proxy-ports.cfg、go proxy-front-port.cfg

## 网络API接口

（虽然只有内网才能访问，但还是不公开了吧）

1、获取所有的机器：xxxx

2、根据机器名获取上面所有的Group：xxxx

3、根据GroupName获取部署了该Group的所有机器：xxxx

<br/>

# 三种环境

​    XG、WG（里面包括特殊的和正常的）

<br/>

# 数据的初始化

读取四个配置文件，抽出来

1、所有org和Group的集合：Map\<String,String>，key是orgName，value是GroupName。

2、所有Group和org的集合：Map\<String,String>，key是GroupName，value是orgName。

3、所有集合和上面所有org的集合：Map\<String,List\<String>>，key是MachineName，vallue是该机器上所有org的orgName的集合。

4、机器分组：Map\<String,Integer>,key是MachineName，valuie是机器组的id

<br/>

## 程序的触发

```java
 MainServer mainTask = new MainServer();

//求出当前之间距离明天中午11点整的时间间隔
LocalDateTime d = LocalDateTime.now();
LocalDateTime target = LocalDateTime.of(d.getYear(), d.getMonth(), d.getDayOfMonth(), 11, 00, 00, 00);
target = target.plusDays(1);
Duration duration = Duration.between(d, target);

//计算从当前时间到最近一次执行时间的时间间隔
long delay = duration.toMillis();

//执行周期为一天
long period = 24 * 60 * 60 * 1000;

//使用Java的ScheduledExecutor实现任务调度。每天定时执行某项任务
ScheduledExecutorService service = Executors.newScheduledThreadPool(10);
//从现在开始delay毫秒之后，每隔一星期执行一次job1
service.scheduleAtFixedRate(mainTask, delay, period, TimeUnit.MILLISECONDS);
```

每天的中午11点定时执行，抓取前一天11：00~11：05的数据，进行分析

<br/>

# 核心思路

- 获取所有的机器信息，并排序。

- 将上述信息根据机器所在的机器组进行分组，生成Map\<Integer, List\<SpecialHost\>\>

- 将分好组的信息按照 XG、WG两个大环境，拆分成两个List\<Map.Entry\<Integer, List\<SpecialHost\>\>\>

- 过滤出每组负载率最高和最低的组，并输出相应的信息。

- 如果某一组的负载比率高于70%，那么触发核心程序。给出一定的解决方案：

  - 如果该机器组上只有一个org，那么触发扩展org的程序
  - 如果该机器组属于特殊的那块（QPS为1万），触发特殊的move程序


- 不满足上述情况，那么触发正常的move程序，同时需要传入environment（XG或WG）

- 返回一个字符串，主程序接收该字符串并拼接部分重要信息。输出给slack

<br/>

# 细节

## 关于org的扩展

```java
/**
 * 根据传来的数据，做合理的扩容
 *
 * @param sickMachines 负载异常的一组机器
 * @param orgName      需要扩展的业务名
 * @param environment  环境，XG 或者 WG
 * @param machines     当前环境下的所有机器
 * @return
 * @throwREADME IOException
 */
public static String expendOrg(List<SpecialHost> sickMachines, String orgName, String environment, LinkedList<Map.Entry<Integer, List<SpecialHost>>> machines){...}
```

- 获取一些重要的配置信息
- 根据orgName找出来那些机器组上面已经部署了，然后在machines上面过滤掉这些机器组
- 找出部署了该业务的机器数、计算该Org的总负载（访问网络）。方便计算
- poll machines队列里面的第一组机器
- 数据的更新
- 输出具体的扩容步骤、输出扩容后目标机器组的负载和之前异常机器组的负载情况

<br/>

## 针对那部分特殊的Machine做move

```java
/**
 * 这属于特殊的挪动方式。 专门针对 WG 下面那些负载不能高于10000的机器
 *
 * @param sickMachines 异常的机器组
 * @param orgs         上面的所有业务的集合
 * @param machines     能被移动的机器组的集合
 * @return
 */
private static String specialMove(List<SpecialHost> sickMachines, LinkedList<Org> orgs, LinkedList<Map.Entry<Integer, List<SpecialHost>>> machines){...}
```

- 计算目前orgs的总负载（异常机器组上的一台）
- 循环（直到异常机器组的负载小于70%）
- 找出orgs里面适合被移动的org，移动到machines里面的机器组 poll() 
- 数据的更新
- 输出具体的移动步骤，以及移动之后目标机器组和异常机器组上面的负载情况

<br/>

## 正常情况下的move

```java
/**
     * 针对传入的org和machines给出合理的方案
     *
     * @param sickMachines    异常的机器组
     * @param orgs            异常机器组上面的所有业务
     * @param machines        所有的机器
     * @param machineWithOrgs 配置文件：机器和机器上所有的orgs
     * @param type            类型。0表示是"xg",1表示是"wg"
     * @return
     */
private static String moveOrgsToMachines(
  List<SpecialHost> sickMachines, LinkedList<Org> orgs, 
  LinkedList<Map.Entry<Integer, List<SpecialHost>>> machines, 
  Map<String, List<String>> machineWithOrgs, 
  int type) 
{...}
```

- 计算目前orgs的总负载（异常机器组上的一台）
- 获取阈值 max
- 将移不动的某些org放到队列的尾部
- 循环 while（TRUE），直到异常机器组里面的机器负载小于max
  - 如果小于max，退出
  - 如果machines空了，max的值扩大，并且重新获取machines
  - 获得目标机器组：machines.poll()
  - 找出orgs里面适合移动的orgs集合：subOrgs
    - 如果目标机器组已经存在该org，那么contine
    - 如果放不下了，break；
    - 更新数据
  - 目标机器组新增subOrgs，原orgs移除subOrgs
  - 更新数据 
- 输出信息，返回结果

<br/>

# 用到的技术

- Java8函数式编程
- 任务调度
- fastjson
- httpclient
- 集合类
- 多线程
- SOA
- netty
- LocalDateTime
- Map.compute
- Map.reduce

# 总结

​	因为需求一直在改，所以才搞了差不多一个月。不过中间还是学到了不少东西，特别是函数式编程以及Java各种集合类的使用。不过比起这些，最重要的是提高了写代码的能力，从最开始被老大嫌弃代码写得太丑到后面会夸写得不错，中间还是付出过很多的努力的。在这里感谢老大。

​	实习目前才进行到一半的样子，接下来估计要学的东西更多了。而且最近小组有位很棒的小伙伴离职了，估计组内的开发任务会增重不少，希望自己尽快熟悉DAL的所有业务，为核心项目贡献自己的一份力。最后，祝那位小伙伴前程似锦。顺便，希望自己接下来的实习生活能学到更多。

​	最后，立个Flag。工作稳定之后，我要花三个月时间自学完研究生（数据库方向）的所有课程。