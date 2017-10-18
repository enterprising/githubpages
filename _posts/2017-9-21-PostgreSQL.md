---
layout: post
title: PostgreSQL相关（持续更新）
date: 2017-9-21 17:00
categories: PG
tags: [PG]
---

关于那些SQL语句就不记录了，MySQL和PostgreSQL是基本上一样的。SQL其实跟数据库无关。

一、关于查询

在PG里面，直接 show table; 是没有效果的。

查询所有表：正确姿势是：``\dt`` 或者 ``SELECT * FROM pg_catalog.pg_tables`` 

查询所有数据库：``l`` 或者 ``el``

二、关于退出psql

在Mac端，MySQL可以使用 ``quit;`` 退出。PostgreSQL是退出命令是：``\q``

三、关于连接远程PG数据库

psql -Uxxx（用户名） -h127.0.0.1（远程ip地址） -pxxx（端口号） -dxxx（数据库名） -W（回车输入密码）

更加详细的内容需要：psql - - help，看提示就知道了。

四、切换数据库

postgres=# \c exampledb