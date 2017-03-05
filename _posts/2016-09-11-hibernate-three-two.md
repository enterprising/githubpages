---
layout: post
title: Hibernate(3.2)＿Hibernate多表的操作
date: 2016-09-11 11:18
categories: Hibernate
tags: Hibernate
---
* content
{:toc}
### 简介
　　我们都已经知道了Hibernate对于单表单记录的基本操作了，但是我们知道，在这个世界上，事物与事物之间是有联系的，而且关系是错综复杂的，不仅有一对一，一对多，多对一还有多对多，对于数据库中的数据也是如此，在通常的情况下，对数据库中的带有各种不同关系的数据进行操作是常有的事情，于是我们开始尝试使用Hibernate来进行多个表之间有关系的数据之间的操作。
### 关系
　　我们知道关系有很多种，本文讨论的是其中一对多和多对一的关系，其实这两种关系是相同的，只不过是看对哪一方的对象来说，举一个例子，公司里面有很多部门，也有很多员工，那么部门和员工是怎么组织起来的呢？一个部门领导着多个员工，多个员工同属于一个部门

![](http://upload-images.jianshu.io/upload_images/1796819-a4d35a984c405ee6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　这就是一个经典的一对多关系的例子
### 数据库准备
　　数据库的建立不再赘述，简单说一下表的准备，这里我们需要用到两张表，一张部门表，一张员工表


![部门表](http://upload-images.jianshu.io/upload_images/1796819-92ea59863d222d6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![员工表](http://upload-images.jianshu.io/upload_images/1796819-a034d9b11f2ccdf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　然后填入相关的预设数据

![部门表](http://upload-images.jianshu.io/upload_images/1796819-0f2474183bb72f6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![员工表](http://upload-images.jianshu.io/upload_images/1796819-7a002ae053b4e411.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　员工表外键设置

![员工表外键设置](http://upload-images.jianshu.io/upload_images/1796819-6f1fbb143336eb8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### JavaBean类和映射准备
　　这里我们就对应数据库建立两个JavaBean类Dep和Emp

　　Dep.java：
```java
package com.pojo;

import java.util.HashSet;
import java.util.Set;

public class Dep {
    private int did;
    private String dname;
    private Set<Emp> emps = new HashSet<Emp>();

    public int getDid() {
        return did;
    }

    public void setDid(int did) {
        this.did = did;
    }

    public String getDname() {
        return dname;
    }

    public void setDname(String dname) {
        this.dname = dname;
    }

    public Set<Emp> getEmps() {
        return emps;
    }

    public void setEmps(Set<Emp> emps) {
        this.emps = emps;
    }

}
```
　　Emp.java：
```java
package com.pojo;

public class Emp {
    private int eid;
    private String ename;
    private Dep dep;

    public int getEid() {
        return eid;
    }

    public void setEid(int eid) {
        this.eid = eid;
    }

    public String getEname() {
        return ename;
    }

    public void setEname(String ename) {
        this.ename = ename;
    }

    public Dep getDep() {
        return dep;
    }

    public void setDep(Dep dep) {
        this.dep = dep;
    }

}
```
　　然后是各自对应的Hibernate映射文件
　　Dep.hbm.xml：
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
    <class name="com.pojo.Dep" table="dep">
        <id name="did" column="did">
            <generator class="assigned"></generator>
        </id>
        <property name="dname" column="dname"></property>
        <set name="emps">
            <key column="did"></key>
            <one-to-many class="com.pojo.Emp" />
        </set>
    </class>
</hibernate-mapping>
```
　　Emp.hbm.xml：
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
    <class name="com.pojo.Emp" table="emp">
        <id name="eid" column="eid">
            <generator class="assigned"></generator>
        </id>
        <property name="ename" column="ename"></property>
        <many-to-one name="dep" column="did"></many-to-one>
    </class>
</hibernate-mapping>
```
　　当然最后不要忘了在全局配置文件中加上指定配置文件的命令
```xml
<mapping resource="com/pojo/Dep.hbm.xml"/>
<mapping resource="com/pojo/Emp.hbm.xml"/>
```
### 测试
　　至此准备工作就完成了，接下来我们就可以在Test类中进行测试
　　先简单地尝试一下看看外键的连接是否正确

　　Test.java
```java
package com.test;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

import com.pojo.Dep;
import com.pojo.Emp;

public class Test1 {

    public static void main(String[] args) {
        /* 使用hibernate之前的准备 */
        Configuration configuration = new Configuration().configure();
        SessionFactory factory = configuration.buildSessionFactory();
        Session session = factory.openSession();
        Transaction transaction = session.beginTransaction();

        /* 这里我们获得eid为1的emp对象，然后获得对应的dep对象并输出相关属性 */
        Emp emp = (Emp) session.get(Emp.class, 1);
        Dep dep = emp.getDep();
        System.out.println(emp.getEid() + "\t" + emp.getEname() + "\t"
                + dep.getDname());
    }

}
```
　　运行之后可以看到控制台输出

![](http://upload-images.jianshu.io/upload_images/1796819-48235632eb42ca79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　说明我们的Hibernate没有配置错误

　　**增**
　　任务：新增一个部门并向这个部门中新增员工
　　Test2.java
```java
package com.test;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

import com.pojo.Dep;
import com.pojo.Emp;

public class Test2 {

    public static void main(String[] args) {
        /* 使用hibernate之前的准备 */
        Configuration configuration = new Configuration().configure();
        SessionFactory factory = configuration.buildSessionFactory();
        Session session = factory.openSession();
        Transaction transaction = session.beginTransaction();

        /* 新建一个部门并赋值，然后保存到数据库 */
        Dep dep = new Dep();
        dep.setDid(3);
        dep.setDname("财务部门");
        session.save(dep);
        
        /* 新建员工1，保存到数据库 */
        Emp emp1 = new Emp();
        emp1.setEid(4);
        emp1.setEname("小龙");
        emp1.setDep(dep);
        session.save(emp1);
        
        /* 新建员工2，保存到数据库 */
        Emp emp2 = new Emp();
        emp2.setEid(5);
        emp2.setEname("小蓝");
        emp2.setDep(dep);
        session.save(emp2);
        
        /* 提交事务，然后关闭 */
        transaction.commit();
        session.close();
        
    }

}
```
　　然后可以看到控制台已经打印出了相关的sql语句，并且没有异常出现，说明执行成功

![](http://upload-images.jianshu.io/upload_images/1796819-cc466e526d47c954.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　然后查看数据库，相应的数据已经被保存下来了

![部门表](http://upload-images.jianshu.io/upload_images/1796819-47d934c8b1fd916f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![员工表](http://upload-images.jianshu.io/upload_images/1796819-b2e1aa312e7d7e7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　　**删**
　　我们知道在数据库中如果要删除带外键的表，首先就要删除子表，不然删除会出错，因为子表还在引用父表的时候，不能直接删除父表，在Hibernate中也是同样

　　Test3.java：
```java
package com.test;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;
import org.hibernate.cfg.Configuration;

import com.pojo.Dep;
import com.pojo.Emp;

public class Test3 {

    public static void main(String[] args) {
        /* 使用hibernate之前的准备 */
        Configuration configuration = new Configuration().configure();
        SessionFactory factory = configuration.buildSessionFactory();
        Session session = factory.openSession();
        Transaction transaction = session.beginTransaction();

        Dep dep = (Dep) session.get(Dep.class, 1);
        session.delete(dep);
        
        /* 提交事务，然后关闭 */
        transaction.commit();
        session.close();
        
    }

}
```
　　可以看到控制台一片血红

![控制台](http://upload-images.jianshu.io/upload_images/1796819-8848dfc4c4164cc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
　　可以看到直接删除父表，程序会报org.hibernate.exception.ConstraintViolationException的异常，在以前的sql语句中，如果要删除父表，就必须删除所有外键关联父表的元素，才可以删除父表数据，下面的例子就是传统的删除方式