---
layout: post
title: ON DUPLICATE KEY UPDATE
date: 2017-10-17 19:30
categories: 数据库
tags: [数据库]
---



# 说明

on duplicate key update，是MySQL所支持的一种高级语法。（亲测，PG不支持）

主要用途是： 在插入之前进行判断，看将要插入的这条数据是否已经在数据库存在。如果存在，就执行 update 后面的更新语句。主要目的就是，去重，更新。

判断的依据：主键、索引。

# 用法

>**insert into xxx values(x,'x') on duplicate key update x='xx';**

先看当前数据，便于对比。

```java
mysql> select * from tan;
+----+------+
| id | name |
+----+------+
|  1 | p    |
|  2 | s    |
|  3 | j    |
|  4 | t    |
+----+------+
4 rows in set (0.00 sec)
```

插入一条已经存在的数据：

```java
mysql> insert into tan values(1,'p') on duplicate key update name='tp';
Query OK, 2 rows affected (0.00 sec)

mysql> select * from tan;
+----+------+
| id | name |
+----+------+
|  1 | tp   |
|  2 | s    |
|  3 | j    |
|  4 | t    |
+----+------+
5 rows in set (0.00 sec)
```

ID=1这条数据已经存在了，所以理论上我们插入会失败，然后报错退出。

但我们加了 on duplicate key update 之后，它会按我们的设置，对那条已存在的数据进行修改。（当然，不存在那就不触发。）

# 其它

replace语法，其实也能达到上述的目的。

不过策略不同，这种语法会自动查询主键或索引冲突，如有冲突，他会先删除原有的数据记录，然后执行插入新的数据。

它的用法是：

```java
replace into tan values(1,'p')
```

网上说，在大量的更新操作时，replace 比 ON DUPLICATE KEY UPDATE 慢很多。

# 参考资料

1. [Mysql replace 与　insert on duplicate效率分析](https://segmentfault.com/a/1190000002527333)
2. [mysql "ON DUPLICATE KEY UPDATE" 语法](https://my.oschina.net/iceman/blog/53735)