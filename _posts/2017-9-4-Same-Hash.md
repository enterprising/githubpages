---
layout: post
title: 一致性Hash算法
date: 2017-9-4 17:00
categories: 数据库
tags: [数据库]
---

* content
{:toc} 
一致性Hash一般是和Sharding一起用的。

关于Sharding的介绍：<http://blog.tanpeng.net/2017/08/01/Sharding/>

# Hash的介绍

其实它的真实名字叫做散列，哈希只是音译的。哈希函数又叫散列函数，一般是用取余的方法，但还有乘法取整法、平方取中法等。还有，哈希是可能存在冲突的，至于怎么解决。看我之前的博客。这里不是重点，不展开解释。

# Sharding中的Hash

先不考虑十分复杂的散列函数，就直接用取余的方法。

目的：让数据比较均匀的分发到多台服务器上。

一般可以用主键来Hash（一维Sharding）、联合主键如用户id+商户id（二维Sharding）。

# 不一致性Hash算法

假设我们有两台机器，我们需要把User表水平切分映射到这两台机器。

一个简单的想法就是 userID % 2，余数0，1 对应两台机器的编号。

这样看上去没问题，但如果加一台机器呢？2变成3，那么之前很多数的余数就变了，要进行大量的数据迁移。所以说，这种方法的扩展性非常差。

这种Hash算法叫做不一致性Hash算法。

# 一致性Hash算法

和我同岁，由麻省理工的人发明的。

## 简述

具体在计算一致性hash时采用如下步骤：

1. 首先求出memcached服务器（节点）的哈希值，并将其配置到0～232的圆（continuum）上。
2. 然后采用同样的方法求出存储数据的键的哈希值，并映射到相同的圆上。
3. 然后从数据映射到的位置开始顺时针查找，将数据保存到找到的第一个服务器上。如果超过232仍然找不到服务器，就会保存到第一台memcached服务器上。

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash1.png)

从上图的状态中添加一台memcached服务器。余数分布式算法由于保存键的服务器会发生巨大变化而影响缓存的命中率，但Consistent Hashing中，只有在圆（continuum）上增加服务器的地点逆时针方向的第一台服务器上的键会受到影响，如下图所示：

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash2.png)

## 原理

我的理解，主要是虚拟出来一个环，环上有很多点。为了方便理解，我们先将每个点看做一台服务器（其实真实情况是一台服务器对应很多个点，为了解决数据倾斜或者说不均匀的情况）。

某个数据要过来，先通过Hash函数确定它在哪个点，然后顺时针找到第一个有服务器的节点。

当新增一台服务器的时候，将新的点插入到两点之间。这样只会影响到新点逆时针的第一个点。

达到一致性Hash的效果。

具体说明：（以下内容转自参考资料1）

一致性哈希算法（Consistent Hashing）最早在论文《[Consistent Hashing and Random Trees: Distributed Caching Protocols for Relieving Hot Spots on the World Wide Web](http://dl.acm.org/citation.cfm?id=258660)》中被提出。简单来说，一致性哈希将整个哈希值空间组织成一个虚拟的圆环，如假设某哈希函数H的值空间为0-2^32-1（即哈希值是一个32位无符号整形），整个哈希空间环如下：

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash3.png)

整个空间按顺时针方向组织。0和232-1在零点中方向重合。

下一步将各个服务器使用Hash进行一个哈希，具体可以选择服务器的ip或主机名作为关键字进行哈希，这样每台机器就能确定其在哈希环上的位置，这里假设将上文中四台服务器使用ip地址哈希后在环空间的位置如下：

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash4.png)

接下来使用如下算法定位数据访问到相应服务器：将数据key使用相同的函数Hash计算出哈希值，并确定此数据在环上的位置，从此位置沿环顺时针“行走”，第一台遇到的服务器就是其应该定位到的服务器。

例如我们有Object A、Object B、Object C、Object D四个数据对象，经过哈希计算后，在环空间上的位置如下：

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash5.png)

根据一致性哈希算法，数据A会被定为到Node A上，B被定为到Node B上，C被定为到Node C上，D被定为到Node D上。

下面分析一致性哈希算法的容错性和可扩展性。现假设Node C不幸宕机，可以看到此时对象A、B、D不会受到影响，只有C对象被重定位到Node D。一般的，在一致性哈希算法中，如果一台服务器不可用，则受影响的数据仅仅是此服务器到其环空间中前一台服务器（即沿着逆时针方向行走遇到的第一台服务器）之间数据，其它不会受到影响。

下面考虑另外一种情况，如果在系统中增加一台服务器Node X，如下图所示：

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash6.png)

此时对象Object A、B、D不受影响，只有对象C需要重定位到新的Node X 。一般的，在一致性哈希算法中，如果增加一台服务器，则受影响的数据仅仅是新服务器到其环空间中前一台服务器（即沿着逆时针方向行走遇到的第一台服务器）之间数据，其它数据也不会受到影响。

综上所述，一致性哈希算法对于节点的增减都只需重定位环空间中的一小部分数据，具有较好的容错性和可扩展性。

另外，一致性哈希算法在服务节点太少时，容易因为节点分部不均匀而造成数据倾斜问题。例如系统中只有两台服务器，其环分布如下，

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash7.png)

此时必然造成大量数据集中到Node A上，而只有极少量会定位到Node B上。为了解决这种数据倾斜问题，一致性哈希算法引入了虚拟节点机制，即对每一个服务节点计算多个哈希，每个计算结果位置都放置一个此服务节点，称为虚拟节点。具体做法可以在服务器ip或主机名的后面增加编号来实现。例如上面的情况，可以为每台服务器计算三个虚拟节点，于是可以分别计算 “Node A#1”、“Node A#2”、“Node A#3”、“Node B#1”、“Node B#2”、“Node B#3”的哈希值，于是形成六个虚拟节点：

![](https://troywu0.gitbooks.io/spark/content/assets/consistanthash8.png)

同时数据定位算法不变，只是多了一步虚拟节点到实际节点的映射，例如定位到“Node A#1”、“Node A#2”、“Node A#3”三个虚拟节点的数据均定位到Node A上。这样就解决了服务节点少时数据倾斜的问题。在实际应用中，通常将虚拟节点数设置为32甚至更大，因此即使很少的服务节点也能做到相对均匀的数据分布。

# 参考资料

1. [一致性Hash](https://troywu0.gitbooks.io/spark/content/%E4%B8%80%E8%87%B4%E6%80%A7hash.html)
2. [数据库sharding和一致性哈希算法](http://www.voidcn.com/article/p-mkgksbor-eo.html)