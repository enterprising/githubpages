---
layout: post
title: Hibernate(3.0)＿Hibernate单表的增删改查
date: 2016-09-06 11:04
categories: Hibernate
tags: Hibernate
---
* content
{:toc}
　　在项目中搭建好了Hibernate框架之后，就可以直接用Hibernate进行数据库的一些操作了
### 搭建数据库
　　首先先在数据库中搭建一个简单的测试数据库
　　新建一个新的数据库Hibernate_Project，字符集选UTF-8，排序默认

![](http://upload-images.jianshu.io/upload_images/1796819-70d4c2462c337027.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　然后新建一张表

![](http://upload-images.jianshu.io/upload_images/1796819-0010816b56f04b7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　并保存为Student

![](http://upload-images.jianshu.io/upload_images/1796819-6d189d9d55d6aa89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　然后在表中填入测试用的数据

![](http://upload-images.jianshu.io/upload_images/1796819-c694e572cc614b59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　新建好数据库之后在hibernate.cfg.xml中修改相应的数据库URL
```xml
<property name="connection.url">
    jdbc:mysql://127.0.0.1:3306/Hibernate_Project
</property>
```
### 编写代码
　　搭建好数据库之后就可以回到项目中开始写相应的代码
　　首先先写一个对应数据库数据结构的JavaBean文件，并添加getter和setter

Student.java
```java
package com.pojo;

public class Student {

    private int stuid;
    private String stuname;

    public int getStuid() {
        return stuid;
    }

    public void setStuid(int stuid) {
        this.stuid = stuid;
    }

    public String getStuname() {
        return stuname;
    }

    public void setStuname(String stuname) {
        this.stuname = stuname;
    }

}
```
　　然后再pojo包下建立一个JavaBean文件的Hibernate映射文件

Student.hbm.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
    <!-- 指定数据库对应的JavaBean的class文件 -->
    <!-- name是类名(类名要加上包名) table是数据库对应的表 -->
    <class name="com.pojo.Student" table="student">
        <!-- id是指定主键 column是数据库对应的列名 name是类中对应主键的属性名 -->
        <id column="stuid" name="stuid">
            <!-- 指定主键的生成方式是由用户生成主键值 -->
            <generator class="assigned"/>
        </id>
        <!-- 下面是各个对应的非主键属性与Bean类中的属性一一对应 -->
        <property column="stuname" name="stuname"/>
    </class>
</hibernate-mapping>
```
### 注：
　　事实上关于配置文件还是有很多别的写法，以上的写法是比较标准和全面的写法，但是Hibernate提供了一些约定俗成的简略写法，在一定程度上可以使写配置文件更有效率
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<!-- 可以在一开始就指定包名，下面类名指定的时候就可以省略包名的部分了 -->
<hibernate-mapping　package="com.pojo">
    <!-- 包名的部分可以省略掉，然后如果类名和数据库的表名相同，那么table属性也可以省略 -->
    <class name="Student" >
        <!-- 省略情况同上 -->
        <id name="stuid">
            <!-- 指定主键的生成方式是由用户生成主键值 -->
            <generator class="assigned"/>
        </id>
        <!-- 省略情况同上 -->
        <property name="stuname"/>
    </class>
</hibernate-mapping>
```
### 测试基本操作
　　至此，项目中的基本配置已经配置好了，接下来可以开始写Test测试方法了。

![](http://upload-images.jianshu.io/upload_images/1796819-f5e3112f0b4e118a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Test.java
```java
package com.pojo;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class Test {

    public static void main(String[] args) {
        // 1.读取总配置文件
        Configuration configuration = new Configuration().configure();
        // 2.创建Session工厂
        SessionFactory factory = configuration.buildSessionFactory();
        // 3.得到Session session其实就是对connection对象的封装
        Session session = factory.openSession();
        // 4.开启事务 ACID 原子性 一致性 隔离性 持久性
        Transaction transaction = session.beginTransaction();
        // 5.持久化操作
        Student student = new Student();
        student.setStuid(3);
        student.setStuname("damao");
        session.save(student);
        // 6.提交事务
        transaction.commit();
        // 7.关闭session
        session.close();
    }

}
```
　　其中的持久化操作就是对数据库的实际操作，可以替换成项目需要的操作
### 增
```java
//新建一个Student类
Student student = new Student();
//指定其中的各个属性的值
student.setStuid(3);
student.setStuname("xiaohei");
//用save方法保存在内存中，然后用transaction.commit()提交事务，数据库便把数据存下来了
session.save(student);
```

![](http://upload-images.jianshu.io/upload_images/1796819-3aad27f7e8332413.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　可以看到在控制台已经把sql语句打印出来了，表示已经执行成功了，然后再看数据库中是否把新的数据增加到表里

![](http://upload-images.jianshu.io/upload_images/1796819-b65827c82d62a001.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　可以看到数据已经被添加到表中了
##### 中文乱码
　　然而很多时候我们需要往数据库里面添加中文，比如用户名之类的属性，这个时候我们直接插入中文数据的时候，我们把上面的姓名一项改成中文
```java
student.setStuname("小黑");
```
　　再次执行就会发现数据库中出现了不能显示的乱码

![](http://upload-images.jianshu.io/upload_images/1796819-f958ef2d60c33a94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　这是因为Hibernate和MySQL默认的操作字符集不是我们常用的UTF-8所以就会出现乱码，这个时候我们需要去修改Hibernte的全局配置文件中的数据库URL，在原来的URL尾巴上加上字符指定代码
```xml
<property name="connection.url">
        jdbc:mysql://127.0.0.1:3306/Hibernate_Project?useUnicode=true&characterEncoding=UTF-8
    </property>
```
　　这样再添加中文就不会出现乱码了

![](http://upload-images.jianshu.io/upload_images/1796819-d7018f6f27ca3667.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 删
　　只需要基于增加的方法改为
```java
Student student = (Student) session.load(Student.class, 1);
session.delete(student);
```

![](http://upload-images.jianshu.io/upload_images/1796819-246cf524eabb00f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　我们可以看到数据库中stuid为1的记录已经被删掉了

![](http://upload-images.jianshu.io/upload_images/1796819-7389a5b4fa5f4282.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 改
　　同上简单修改代码为
```java
Student student = (Student) session.load(Student.class, 2);
student.setStuname("赵六");
session.update(student);
```

![](http://upload-images.jianshu.io/upload_images/1796819-d6eb5ca04c97fd77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　可以看到数据库中stuid的stuname属性已经被修改

![](http://upload-images.jianshu.io/upload_images/1796819-2bec65e13aec684e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 查
　　同上简单修改代码为
```java
Student student = (Student) session.load(Student.class, 2);
System.out.println(student.getStuid());
System.out.println(student.getStuname());
```
　　可以看到控制台打印出了stuid为2的记录的信息（这个顺序有点凌乱我也不知道为毛，不过总算是打印出来了）

![](http://upload-images.jianshu.io/upload_images/1796819-d281a94f21e0610f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
　　以上便是简单的单表单记录的增删改查，复杂的以后慢慢再补充