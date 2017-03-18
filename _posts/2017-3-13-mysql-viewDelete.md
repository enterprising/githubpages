---
layout: post
title: 视图在哪些时候不能更新
date: 2017-3-13 22:49
categories: 数据库
tags: [数据库]
---

## 视图包含下列结构是不可以更新的：
> 1、集合运算符，union, union all, intersect, minus  
2、distinct关键字  消除重复数据  
3、group by / order by / connect by或者start with  
4、子查询  
5、分组函数  
6、需要更新的列不是视图定义的  
7、具有连接查询  
8、违反基表的约束条件，连接视图是指基于多表查询创建的视图。（ 一般不容易修改，但通用instead of触发器可以实现修改的功能）  
