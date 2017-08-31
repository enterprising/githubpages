---
layout: post
title: 负载均衡
date: 2017-8-31 11:00
categories: 架构
tags: [架构]
---

* content
{:toc} 
# 定义

负载均衡（Load Balance）是一种计算机网络技术，用来在多个计算机（计算机集群）、网络连接、CPU、磁盘或其他资源中分配负载，以达到最佳化资源使用、最大化吞吐率、最小化响应时间，同时避免过载的目的。

​										     				          	\- \- \-  摘自维基百科												

简单的说就是：分发请求流量到不同的服务器，使得整个集群的负载处于均衡状态。

# 实现方式

主要有两种实现方式：1、服务器端实现，2、客户端实现。

## 服务器端实现

![](https://skyao.gitbooks.io/learning-microservice/content/implementation/core/loadbalancer/images/style_server.png)

主要是在中间加了一层代理服务器，用户请求到中间层的负载均衡服务器，由负载均衡服务器分发控制到真实提供服务的应用服务器。服务器端维护负载均衡服务器，控制负载均衡策略和算法。

- 软件

  - **Ngnix**
  - HA Proxy
  - Apache
  - **LVS**

- 硬件

  - F5
  - NSX
  - BigIP

  ​

## 客户端实现

![](https://skyao.gitbooks.io/learning-microservice/content/implementation/core/loadbalancer/images/style_client.png)

客户端负载均衡是指负载均衡器作为客户端软件的一部分，客户端得到可用的服务器列表然后按照特定的负载均衡策略，分发请求到不同的服务器端。

重点：

1. 对客户端不透明的：客户端需要知道服务器端的服务列表，需要自行决定请求要发送的目标地址
2. 客户端维护负载均衡服务器，控制负载均衡策略和算法

目前单独提供的客户端实现比较少，只有:

- Netflix Ribbon

大部分都是在框架内部自行实现。

# 三种服务器集群

- 高性能集群：将单个重负载的请求分散到多个节点进行处理，最后再将处理结果进行汇总
- 高可用集群：提高冗余单元，避免单点故障
- 负载均衡集群：**将大量的并发请求分担到多个处理节点。由于单个处理节点的故障不影响整个服务，负载均衡集群同时也实现了高可用性。**

# 部分实现方式详解

## HTTP重定向

![](https://pic4.zhimg.com/v2-7c9156e028659ea164d0d92e69c88383_b.jpg)

Web服务器可通过Http响应头信息中的Location标记来返回一个新的URL，浏览器自动去访问这个新的URL。

实现：可以通过Web应用程序代码实现你想到的调度策略，如可根据请求的URL的不同来进行合理的过滤和转移。

## DNS负载均衡

![](https://pic4.zhimg.com/v2-3b2dc25ad7ddf0ae86992c2972fabbd3_b.jpg)

一个域名可以绑定到多个IP地址，一对多的关系。

DNS负责将用户请求的域名映射为实际的IP地址，这种映射可以是一对多的（ DNS的A记录，用来指定域名对应的IP地址），这样DNS服务器便充当负载均衡调度器。

DNS节省了所谓的主站点，DNS服务器已经充当了主站点的职能。常见的策略是对多个A记录进行RR(轮询) 

## 反向代理负载均衡

Nginx

![](https://pic2.zhimg.com/v2-843cbee5c1f776922a73305ddd597f01_b.jpg)

反向代理服务器的核心工作是转发**HTTP**，它工作在HTTP层面，因此，基于反向代理的负载均衡也称为七层负载均衡。

任何对于实际服务器的HTTP请求都必须经过调度器；调度器必须等待实际服务器的HTTP响应，并将它反馈给用户。

## IP负载均衡

在网关服务器（传输层）修改IP地址或者端口号，达到转发的目的。

![](https://pic2.zhimg.com/v2-7c5cf88cb54a457d96a5fd1edee1d7c5_b.jpg)

网络地址转换(NAT)负载均衡工作在传输层，对数据包中的IP地址和端口进行修改，从而达到转发的目的，称为四层负载均衡。 

NAT服务器（前端服务器）必须作为实际服务器（后端服务器）的网关，否则数据包被转发后将一去不返。 

# 参考资料

1. <https://skyao.gitbooks.io/learning-microservice/content/implementation/core/loadbalancer/>
2. [Web负载均衡的几种实现方式](http://blog.csdn.net/zhoudaxia/article/details/23672319)
3. [维基百科-负载均衡](https://zh.wikipedia.org/wiki/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1)
4. [知乎-服务器集群负载均衡原理](https://www.zhihu.com/question/22610352)
5. 《构建高性能Web站点》第12章 web负载均衡  
6. 《大型网站技术架构：核心原理与案例分析》 6.2 应用服务器集群的伸缩性设计