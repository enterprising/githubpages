---
layout: post
title: MySQL引擎对比（InnoDB，MyISAM）
date: 2017-8-13 16:00
categories: 数据库
tags: [数据库]
---

* content
{:toc} 
第一次了解MySQL的引擎，是在大二学习数据库这门课的时候。这门课是班主任上的。当时可能是教学安排的时间有点紧，所以班主任也没怎么讲。之后自己把《MySQL核心技术》里面关于存储引擎的部分看了一遍，收获很大。现在对其中两种最常用的引擎做个整理。

# 简单介绍

我当时的理解的话，**要是有事务那么就要用InnoDB，因为老的MyISAM不支持事务。还有一点，InnoDB支持外键，MyISAM不支持外键。**

如果不清楚自己应该用什么引擎，那么请选择InnoDB，

Mysql5.5+的版本默认引擎都是InnoDB，

早期的Mysql版本默认的引擎是MyISAM

<br/>

# 适用场景

MyISAM：

- 做很多count的计算
- 插入不频繁，查询十分频繁
- 没有事务
- 低版本的MySQL

InnoDB：

- 要求事务，而且可靠性要求较高
- 表更新和查询都相当频繁

<br/>

# 区别

一、锁

MyISAM使用的是 **表锁**，在写比较多的情况下，性能非常差。

InnoDB使用的是 **行级锁**，写的并发度比MyISAM好。

二、事务

MyISAM不支持事务

InnoDB支持事务

三、速度

对于几乎只读的表，MyISAM会快一点。

四、外键

MyISAM不支持外键

**InnoDB支持外键**

<br/>

# 参考资料

1、[Mysql中MyISAM引擎和InnoDB引擎的比较](http://www.cnblogs.com/zlcxbb/p/5757173.html)

2、[关于Mysql的存储引擎InnoDB和MyISAM的对比](http://www.jianshu.com/p/81704965d923)

3、[MyISAM与innodb的对比](http://www.jianshu.com/p/LUEred)