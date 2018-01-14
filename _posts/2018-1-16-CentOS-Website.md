---
layout: post
title: CentOS+MySQL(docker)+Springboot搭建个人网站
date: 2018-1-14 21:00
categories: 服务器
tags: [Linux, MySQL]
---

* content
{:toc} 
写在前面：

首先说一下为啥要重构这个网站。这是因为大四期末的时候遇到一门课设，那么课叫做软件开发新技术。所以，趁着课设的机会，顺便就把自己的网站给重构了一下。这里主要包括几个点，一个是把服务器从Windows换成了Linux... (当年真的是因为水平不够才选择Windows的，现在觉得Linux用起来真的是太爽了)。第二个就是用Spring boot替换了原来十分繁琐的各种SSH框架的配置。

本文主要记录整个过程中遇到的一些坑，而且记录的是服务器搭建过程的坑，代码的坑略过。

# 坑一：腾讯云服务器登录

在后台web页面登录进去，超级卡。所以最好的方式还是使用SSH，直接从终端进行登录。这样会快很多。

注意看官方文档：

<https://cloud.tencent.com/document/product/213/5436>

<http://bbs.qcloud.com/thread-6707-1-1.html>

首先需要根据文档，在控制台生成一个ssh文件，然后下载到本地。

以我这边为例，本地是CentOS，然后登录指令是：

> ssh -i "/data/ssh" root@115.159.112.197

说明： -i 参数是ssh文件的路径，root表示是最高权限用户，@后面带的是你的腾讯云服务器的IP地址

# 坑二：服务器上安装MySQL

emmm… 其实也不算是坑吧，随便搜搜就出来了。我这边的先装了个Docker，然后用Docker装了一个MySQL的镜像。这样非常方便管理。。当然，数据库啥的，最好还是不要Docker化。我这里只是为了练习Docker。

关于Docker的使用，可以去网上搜教程，这里不细述了。

# 坑三：springboot项目的打包

强烈推荐用maven的打包命令，当然，idea本身也有自带的打包工具，但是太繁琐了。还是敲指令方便多了。

打包的命令是：

> mvn clean package

还有mvn clean test，mvn clean install之类的指令，超好用。

# 坑四：上传文件到服务器

这个网上也有一大堆教程，我记录下我的解决方法。

我这边是MacOS系统，在本地用SCP命令进行传输：

> scp  -i "/data/ssh"  /Users/enterprising/IdeaProjects/tanpen/target/tanpen-0.0.1-SNAPSHOT.jar root@115.159.112.197:/home

-i 后面跟着的是ssh文件的路径，然后后面第一个参数是本地文件，第二个参数是服务器上要放这个文件的路径。

# 坑五：nohup指令运行后台进程

当你把打包好的jar包传到服务器后，可以直接用 java -jar命令进行运行。

我当时就是这样做的。。然后发现，每次我一关ssh连接，我网站就访问不了了。后来登上去用 jps 指令去查，发现那个进程已经被杀掉了。

所以这个也是个大坑。解决的方法是使用 nohup 指令。

使用姿势如下：

> nohup java -jar tanpen-0.0.1-SNAPSHOT.jar

-jar后面跟jar的地址。