---
layout: post
title: Hibernate(3.1)＿Hibernate的get和load
date: 2016-09-06 20:50
categories: Hibernate
tags: Hibernate
---
* content
{:toc}
　　我们都知道在要从数据库中得到一个对象，通常有两种方式，一种是通过session.get()方法，另一种就是通过session.load()方法，然后其实这两种方法在获得一个实体对象时是有区别的，在查询性能上两者是不同的。

### load方式
　　当使用load方法来得到一个对象时，此时hibernate会使用延迟加载的机制来加载这个对象，即：**当我们使用session.load()方法来加载一个对象时，此时并不会发出sql语句，当前得到的这个对象其实是一个代理对象，这个代理对象只保存了实体对象的id值，只有当我们要使用这个对象，得到其它属性时，这个时候才会发出sql语句，从数据库中去查询我们的对象。**
```java
Student student = (Student) session.load(Student.class, 2);
System.out.println(student.getStuid());
```
　　执行代码后我们可以看到控制台直接打印出了student对象的stuid，而并没有执行sql语句

![](http://upload-images.jianshu.io/upload_images/1796819-ea13d86644fe0b8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　但是当我们要输出student对象的stuname的时候
```java
Student student = (Student) session.load(Student.class, 2);
System.out.println(student.getStuid());
System.out.println(student.getStuname());
```
　　我们可以看到在输出了stuid的值之后，hibernate执行了sql语句，然后再输出stuname的值

![](http://upload-images.jianshu.io/upload_images/1796819-2bd8d9a11c05fbc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　可以很清楚的看出，使用load()方法得到的对象是一个代理对象，只存了stuid值的临时对象，当得到该对象的其他属性值时候，才会执行相应的sql语句去查询，可以把代理对象想象为一个只有stuid的student对象，除了stuid其他的属性值为空，所以只输出stuid是不需要向数据库查询的，一旦需要输出其他的属性值，hibernate就会立刻向数据库查询所有的属性值，并把代理对象的属性值补充完整。
```java
Student student = (Student) session.load(Student.class, 2);
System.out.println(student.getStuid());
System.out.println(student.getStuname());
System.out.println(student.getAge());
```
![](http://upload-images.jianshu.io/upload_images/1796819-4ad4eed22df400f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　执行上面代码，从控制台可以看到，不管有多少个属性，在输出第一个非主键属性的时候，hibernate就会把全部属性补充完整，也就是在使用非stuid属性的时候执行一次sql语句，之后再使用对象的时候就不需要再查询数据库了。
### get方式
　　与load方式相异的就是在执行session.get()方法的时候，就直接查询数据库把student对象里面的所有属性值都补充完整
```java
Student student = (Student) session.get(Student.class, 3);
```
![](http://upload-images.jianshu.io/upload_images/1796819-8997706c1182e0b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　可以看到即使不输出任何属性，也直接执行了查询的sql语句得到了完整的student对象
### 异常问题
　　当我们使用load和get方法去获取一个不存在的stuid时会怎么样呢？我们试图得到一个stuid为10的对象并输出它的stuname
　　**get**
```java
Student student = (Student) session.get(Student.class, 10);
System.out.println(student.getStuname());
```
　　我们发现我们得到了一个NullPointerException异常
![](http://upload-images.jianshu.io/upload_images/1796819-237b9d65a7c1a916.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　**load**
　　再来看看使用load方法会怎么样
```java
Student student = (Student) session.load(Student.class, 10);
System.out.println(student.getStuname());
```
![](http://upload-images.jianshu.io/upload_images/1796819-ff1fb49aba52fa77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　得到的是一个ObjectNotFoundException异常
　　**这是什么原因？**
　　这是因为通过get方式我们会去数据库中查询出该对象，但是这个stuid值不存在，所以此时student对象是null，所以就会报NullPointException的异常了。
　　使用load时，此时的student对象是一个代理对象，仅仅保存了当前的这个stuid值，当我们试图得到该对象的username属性时，这个属性其实是不存在的，所以就会报出ObjectNotFoundException这个异常了。
　　
　　**关于LazyInitializationException异常**
　　要说这个异常，我们先要模拟使用一个StudentDao的类，Dao类我们是在JDBC时期经常使用的一种方式，将所有和数据库有关的操作放在一个Dao类当中进行调用。

StudentDao.java
```java
package com.pojo;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

public class StudentDao {

    public Student getStudent(int stuid) {
        Student student = null;
        Session session = null;
        Transaction transaction = null;
        try {
            Configuration configuration = new Configuration().configure();
            SessionFactory factory = configuration.buildSessionFactory();
            session = factory.openSession();
            transaction = session.beginTransaction();
            student = (Student) session.load(Student.class, 1);
            transaction.commit();
        } catch (Exception e) {
            e.printStackTrace();
            transaction.rollback();

        } finally {
            session.close();
        }
        return student;
    }

}
```
Test.java
```java
package com.pojo;

public class Test {

    public static void main(String[] args) {
        StudentDao studentDao = new StudentDao();
        Student student = studentDao.getStudent(2);
        System.out.println(student.getStuname());
    }

}
```
　　运行可以发现，我们得到了一个LazyInitializationException异常

![](http://upload-images.jianshu.io/upload_images/1796819-0946d892ecf7f63f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　这个异常的原因也是因为load方法的延迟加载，当我们通过load()方法来加载一个对象时，此时并没有发出sql语句去从数据库中查询出该对象，当前这个对象仅仅是一个只有id的代理对象，我们还并没有使用该对象，但是此时我们的session已经关闭了，所以当我们在测试用例中使用该对象时就会报LazyInitializationException这个异常了。
　　**解决方法**
　　１.将load改成get的方式来得到该对象
　　２.在表示层来开启我们的session和关闭session