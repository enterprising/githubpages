---
layout: post
title: MySQL主从复制（master-slave）和 读写分离（MySQL-Proxy）
date: 2017-8-22 17:00
categories: 数据库
tags: [数据库]
---

* content
{:toc} 
# 为什么要做主从复制

如果对数据库的读和写都在同一个数据库服务器中操作，业务系统的性能就会下降。

主要还是为了提升系统性能，优化用户体验，减轻对主数据库的负载。

一般数据库的读要比写要快，而且读是用得最多的，写相对来说可能要少一点。所以就有了读写分离。有读写分离的话，那不同数据库之间的数据总要同步吧，所以就有了主从复制。

# 框架图

![](http://s2.51cto.com/wyfs02/M00/89/EE/wKiom1ghsBnzeV_OAADmPGxhuAs725.png-wh_500x0-wm_3-wmp_4-s_1418936138.png)

其实在应用服务器到master和slave之间有一层代理，图上没画出来。

# 主从复制（master-slave）

![主从复制](https://ws3.sinaimg.cn/large/006tNc79gy1fislr6bgc8j30go08d0tg.jpg)

MySQL5.6开始主从复制有两种方式：基于日志（binlog）；基于GTID（全局事务标示符）。但后面那个有些老版本的MySQL不支持临时表。

基于日志复制的过程：

分为同步复制和异步复制，实际复制架构中大部分为异步复制。 

1).Slave上面的IO进程连接上Master，并请求从指定日志文件的指定位置（或者从最开始的日志）之后的日志内容；

2).Master接收到来自Slave的IO进程的请求后，通过负责复制的IO进程根据请求信息读取制定日志指定位置之后的日志信息，返回给Slave

的IO进程。返回信息中除了日志所包含的信息之外，还包括本次返回的信息已经到Master端的bin-log文件的名称以及bin-log的位置；

3).Slave的IO进程接收到信息后，将接收到的日志内容依次添加到Slave端的relay-log文件的最末端，并将读取到的Master端的

bin-log的文件名和位置记录到master-info文件中，以便在下一次读取的时候能够清楚的告诉Master“我需要从某个bin-log的哪个位置开始往后的日志内容，请发给我”；

4).Slave的Sql进程检测到relay-log中新增加了内容后，会马上解析relay-log的内容成为在Master端真实执行时候的那些可执行的内容，并在自身执行。

# 读写分离（MySQL-Proxy）

![读写分离](https://ws3.sinaimg.cn/large/006tKfTcgy1fislxjs41rj30dw06daam.jpg)

读写分离就是 在主服务器上修改，数据会同步到从服务器，从服务器只提供读取数据，不能写入。实现备份的同时也实现了数据库性能的优化，以及提升了服务器安全。

proxy是代理的意思，其实读写分离也是代理模式的一种实现。代理服务器接收到应用层传来的请求，先判断再确定要转发到哪里。

目前读写分离主流的代理层：

（1）mysql_proxy。mysql_proxy是Mysql的一个开源项目，通过其自带的lua脚本进行sql判断。

（2）Atlas。是由 Qihoo 360, Web平台部基础架构团队开发维护的一个基于MySQL协议的数据中间层项目。它是在mysql-proxy 0.8.2版本的基础上，对其进行了优化，增加了一些新的功能特性。360内部使用Atlas运行的mysql业务，每天承载的读写请求数达几十亿条。支持事物以及存储过程。

（3）Amoeba。由阿里巴巴集团在职员工陈思儒使用序java语言进行开发，阿里巴巴集团将其用户生产环境下，但是他并不支持事物以及存数过程。

# 参考资料

1、《MySQL5.6主从复制(读写分离)方案》。2016.4。泽晨。<https://yq.aliyun.com/articles/24255>

2、《MYSQL主从复制与读写分离》。2016.11。<http://yw666.blog.51cto.com/11977292/1870808>