---
layout: post
title: PostgreSQL相关（持续更新）
date: 2017-9-21 17:00
categories: PG
tags: [PG]
---

一、关于查询

在PG里面，直接 show table; 是没有效果的。

查询所有表：正确姿势是：``\dt`` 或者 ``SELECT * FROM pg_catalog.pg_tables`` 

查询所有数据库：``l`` 或者 ``el``

