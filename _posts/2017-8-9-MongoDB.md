---
layout: post
title: MongoDB
date: 2017-8-9 20:00
categories: NoSQL
tags: [NoSQL,数据库]
---

* content
{:toc} 
# 什么是MongoDB

MongoDB是一个基于**分布式文件存储**的数据库，由C++编写，旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

所谓的文档存储，其实就是 Python 的字典

<br/>

# 启动和关闭服务

本地环境的话

先配置好环境变量，将安装目录的 bin 配置到 PATH 下面

开启服务：brew services start mongodb

关闭服务：brew services stop mongodb

连接：mongo

<br/>

# 建库和删库跑路

查看所有数据库： show dbs

查看当前数据库：db

使用xx数据库：use xx

创建数据库：use DATABASE_NAME（自己打）

删除数据库：需要先use这个数据库，然后：db.dropDatabase()

<br/>

# 增删改查

（这里都是以文档为单位）：tanpeng 是数据库的name

插入文档：db.tanpeng.insert({“name”:”value”})

删除文档：db.tanpeng.remove({“name”:”tanpeng”} ,  1 )  在后面加1表示只删除第一个符合这个条件的文档

修改文档：db.tanpeng.update({“name”:”tanpeng”},{$set {“name”:”dashuaige"}})  

查询文档：db.tanpeng.find({“name”:”tanpeng”}).pretty()  说明pretty代表格式化输出，find里面加的是条件，当然也可以不加。 注意，里面那些关于数值的比较，不能用符号了，要用那些转义字符。

<br/>

# 关于Mongo+Atlas

见鬼了，加了白名单也连不上！！

我再研究下。

未完待续

<br/>

# 参考资料

1、《MongoDB教程》。<http://www.runoob.com/mongodb/mongodb-tutorial.html>

2、MongoDB官方API。<https://docs.mongodb.com/manual/tutorial/getting-started/>