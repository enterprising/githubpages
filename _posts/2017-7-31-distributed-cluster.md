---
layout: post
title: 分布式和集群
date: 2017-7-31 10:00
categories: 分布式和集群
tags: [分布式,集群]
---

* content
{:toc}
# 定义

分布式：一个业务拆分成多个子业务，部署在不同的服务器上

集群：同一个业务，部署在多台服务器上

<br/>

# 举例

举个例子：

小饭店原来只有一个厨师，切菜洗菜买菜炒菜全包了，后来客人多了，一个厨师忙不过来，又请了一个厨师。

两个厨师都能炒一样的菜，这两个厨师的关系就是集群。

为了让厨师专心炒菜，把菜做到极致，又请了个配菜师帮忙切菜、洗菜，那么这个配菜师和厨师的关系就是分布式

要是一个配菜师忙不过来，又请了一个配菜师，两个配菜师的关系就是集群。

<br/>

# 分布式

分布式系统要解决的问题是**一致性（Consistency）**、**可用性（Availability）**和**分区容错性（Partition Tolerance）**

<br/>

# 集群

三大类型

## 高可用集群

高可用集群（High Availability Cluster）的定义：

简单的说，集群（cluster）就是一组计算机，它们作为一个整体向用户提供一组网络资源。这些单个的计算机系统 就是集群的节点（node）。高可用性集群（HA cluster）是指**如单系统一样地运行并支持（计算机）持续正常运行的一个主机群。**

当主服务器故障的时候，备份服务器能够自动接管主服务器的工作，并及时切换过去，以实现对用户的不间断服务。这也是高可用要关心的问题。

## 负载均衡集群

负载均衡（Load Balance Cluster）的定义：

简单来说就是，在集群环境中，当用户有个请求过来了，该怎么分配它访问那台服务器呢？为了达到资源最大利用，肯定不能有的多有的少对吧？怎么合理分配，这就是负载均衡要解决的问题了。

ngnix，lvs（Linux Virtual Server），HAProxy都是负载均衡组件。

## 科学计算集群

高性能计算集群 (High Perfermance Computing) 的定义：

这类集群致力于提供单个计算机所不能提供的强大的计算能力。

### 高吞吐计算

把一个任务分成若干个可以并行的子任务，而且子任务彼此之间没有什么关联。这类应用的共同特征是：在海量数据上搜索某些模式，所以叫高吞吐计算。

### 分布计算

和高吞吐相反，它们虽然可以分成若干个并行的子任务，但是子任务间联系很紧密，需要大量的数据交换。

<br/>

# 参考资料

1、《分布式和集群》。2017.5。<http://www.jianshu.com/p/b31a61d74c63>

2、《浅谈web应用的负载均衡、集群、高可用(HA)解决方案》。2014.4。<http://aokunsang.iteye.com/blog/2053719>