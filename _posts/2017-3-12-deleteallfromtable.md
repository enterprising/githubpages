---
layout: post
title: 删除一张表中的所有数据
date: 2017-3-12 21:54
categories: 数据库
tags: [数据库]
---


# 若要删除book表中的所有数据，如下哪些语法是错误的？
![1]
  
正确的答案是A和D。
先说说BC，
truncate table book; 删除表中所有数据
delete from book;     删除表中所有数据

A选项，直接把表删了，错误！
D选项，语法错误，没有这种写法。


  [1]: ./images/QQ%E6%88%AA%E5%9B%BE20170312221942.jpg "QQ截图20170312221942"