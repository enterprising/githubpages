---
layout: post
title: Sharding
date: 2017-8-1 17:00
categories: 数据库
tags: [DAL,数据库]
---

* content
{:toc}
# 使用场景

![屏幕快照 2017-07-31 下午5.41.26](https://ws1.sinaimg.cn/large/006tKfTcgy1fi37ilqw7wj30x003o0tw.jpg)

 ------- 摘自阿里巴巴《Java开发手册》

<br/>

# 类型

## 垂直分库

基本思路是：按照业务模块来划分出不同的数据库。

比如：

![垂直分库](http://cdn1.infoqstatic.com/statics_s2_20170801-0326/resource/articles/key-steps-and-likely-problems-of-split-table/zh/resources/21.jpg)

因为数据库本身是“有状态”的，所以相对于Web和应用服务器来说很难做到横向扩展。数据库的连接资源比较宝贵且单机处理能力也有限，在高并发场景下，垂直分库一定程度上能够突破IO、连接数及单机硬件资源的瓶颈，是大型分布式系统中优化数据库架构的重要手段。但是难度也很大。

<br/>

## 垂直分表

大表拆成小表。拆分是基于关系型数据库里面的“列”（字段）。

![垂直分表](http://cdn1.infoqstatic.com/statics_s2_20170801-0326/resource/articles/key-steps-and-likely-problems-of-split-table/zh/resources/20.jpg)

拆分字段的操作建议在数据库设计阶段就做好。如果是在发展过程中拆分，则需要改写以前的查询语句，会额外带来一定的成本和风险，建议谨慎。

<br/>

## 水平分表

就是将表中不同的数据行按照一定的规律分布到不同的数据库表中（这些表保存在同一个数据库中），这样来降低单表的数据量，优化查询性能。

最常见的实现方法就是**通过主键或者时间等字段进行Hash和取模后拆分**。

![水平分表](http://cdn1.infoqstatic.com/statics_s2_20170801-0326/resource/articles/key-steps-and-likely-problems-of-split-table/zh/resources/22.jpg)

水平分表，能够降低单表的数据量，一定程度上可以缓解查询性能瓶颈。但是本质上这些表还保存在同一个数据库中，所以库级别还是会有IO瓶颈。一般**不建议使用**这种策略。

<br/>

## 水平分库分表

在水平分表的基础上，将拆分出来的表保存在不同的数据库中。是现在最流行的做法。

![水平分库分表](http://cdn1.infoqstatic.com/statics_s2_20170801-0326/resource/articles/key-steps-and-likely-problems-of-split-table/zh/resources/23.jpg)

在高并发和海量数据的场景下，分库分表能够有效缓解单机和单库的性能瓶颈和压力，突破IO、连接数、硬件资源的瓶颈。当然，投入的硬件成本也会更高。同时，这也会带来一些复杂的技术问题和挑战（例如：跨分片的复杂查询，跨分片事务等）

<br/>

# 细节

未完待续..

# 难点



# 唯一ID方案

## 利用数据库自增ID

优点：最简单。
缺点：单点风险、单机性能瓶颈。

## Flickr方案

利用数据库集群并设置相应的步长

优点：高可用、ID较简洁。
缺点：需要单独的数据库集群。

## Twitter Snowflake

优点：高性能高可用、易拓展。
缺点：需要独立的集群以及ZK。

## 一大波GUID、Random算法

优点：简单。
缺点：生成ID较长，有重复几率。

## 美团的方案

> 时间戳+用户标识码+随机数

优点：

- 方便、成本低。
- 基本无重复的可能。
- 自带分库规则，这里的用户标识码即为用户ID的后四位，在查询的场景下，只需要订单号就可以匹配到相应的库表而无需用户ID，只取四位是希望订单号尽可能的短一些，并且评估下来四位已经足够。
- 可排序，因为时间戳在最前面。

缺点：长度稍长，性能要比int/bigint的稍差等。

<br/>

# 总结

1、并非所有表都需要水平拆分，要看增长的类型和速度，水平拆分是大招，拆分后会增加开发的复杂度，不到万不得已不使用。

2、在大规模并发的业务上，尽量做到在线查询和离线查询隔离，交易查询和运营/客服查询隔离。

3、拆分维度的选择很重要，要尽可能在解决拆分前问题的基础上，便于开发。

4、尽量使用简单的、良好索引的查询，这样数据库整体可控，也易于长期容量规划以及水平扩展。

<br/>

# 参考资料

1、《分库分表的几种常见形式以及可能遇到的难》。2016.7。<http://www.infoq.com/cn/articles/key-steps-and-likely-problems-of-split-table>

2、《水平分库分表的关键步骤》。2016.11。<http://www.infoq.com/cn/articles/key-steps-and-likely-problems-of-horizontal-split-table>

3、《大众点评订单系统分库分表实践》。2016.11。<https://tech.meituan.com/dianping_order_db_sharding.html>

4、《唯品会订单分库分表的实践总结以及关键步骤》。2016.8。<http://www.infoq.com/cn/articles/summary-and-key-steps-of-vip-orders-depots-table>