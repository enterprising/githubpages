---
layout: post
title: 线程安全的集合
date: 2017-3-13 22:20
categories: Java
tags: [Java,操作系统]
---

## 在java并发编程实战的第五章提到了两个很重要的集合类：
**Vector 、 Hashtable**  重点记住这两个
 > 在这里要特别注意，HashMap不是线程安全的！！还有ArrayList、LinkedList也不是线程安全的

## 线程安全的ConcurrentHashMap
> 关于这个集合类，在java并发编程这本书里讲得特别多，需要特别注意的是：
> ConcurrentHashMap并不是将每个方法都在同一个锁上同步并使得每次只能有一个线程访问容器，而是使用一种粒度更细的枷锁机制来实现更大程度的共享，这种机制称为 **分段锁**
> 使用这个集合类带来的后果是，在并发访问环境下将实现更高的吞吐量，而在单线程环境中只损失非常小的性能。

## 线程安全的StringBuffer，不安全的StringBuilder (以下为转载)
新引入的StringBuilder类不是线程安全的，但其在单线程中的性能比StringBuffer高。如果你对此不太相信，可以试试下面的例子：
```java
package com.hct.test;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
/**
 * @author: chengtai.he
 * @created:2009-12-9 上午09:59:57
 */
public class StringBuilderTester {
 private static final String base = " base string. ";
 private static final int count = 2000000;

 public static void stringTest() {
  long begin, end;
  begin = System.currentTimeMillis();
  String test = new String(base);
  for (int i = 0; i < count/100; i++) {
   test = test + " add ";
  }
  end = System.currentTimeMillis();
  System.out.println((end - begin)
    + " millis has elapsed when used String. ");
 }

 public static void stringBufferTest() {
  long begin, end;
  begin = System.currentTimeMillis();
  StringBuffer test = new StringBuffer(base);
  for (int i = 0; i < count; i++) {
   test = test.append(" add ");
  }
  end = System.currentTimeMillis();
  System.out.println((end - begin)
    + " millis has elapsed when used StringBuffer. ");
 }

 public static void stringBuilderTest() {
  long begin, end;
  begin = System.currentTimeMillis();
  StringBuilder test = new StringBuilder(base);
  for (int i = 0; i < count; i++) {
   test = test.append(" add ");
  }
  end = System.currentTimeMillis();
  System.out.println((end - begin)
    + " millis has elapsed when used StringBuilder. ");
 }

 public static String appendItemsToStringBuiler(List list) {
  StringBuilder b = new StringBuilder();

  for (Iterator i = list.iterator(); i.hasNext();) {
   b.append(i.next()).append(" ");
  }

  return b.toString();
 }

 public static void addToStringBuilder() {
  List list = new ArrayList();
  list.add(" I ");
  list.add(" play ");
  list.add(" Bourgeois ");
  list.add(" guitars ");
  list.add(" and ");
  list.add(" Huber ");
  list.add(" banjos ");

  System.out.println(StringBuilderTester.appendItemsToStirngBuffer(list));
 }

 public static String appendItemsToStirngBuffer(List list) {
  StringBuffer b = new StringBuffer();

  for (Iterator i = list.iterator(); i.hasNext();) {
   b.append(i.next()).append(" ");
  }

  return b.toString();
 }

 public static void addToStringBuffer() {
  List list = new ArrayList();
  list.add(" I ");
  list.add(" play ");
  list.add(" Bourgeois ");
  list.add(" guitars ");
  list.add(" and ");
  list.add(" Huber ");
  list.add(" banjos ");

  System.out.println(StringBuilderTester.appendItemsToStirngBuffer(list));
 }

 public static void main(String[] args) {
  stringTest();
  stringBufferTest();
  stringBuilderTest();
  addToStringBuffer();
  addToStringBuilder();
 }
}
```
>上面的程序结果如下：  
>5266 millis has elapsed when used String.   
>375 millis has elapsed when used StringBuffer.   
>281 millis has elapsed when used StringBuilder.   
> I   play   Bourgeois   guitars   and   Huber   banjos  
> I   play   Bourgeois   guitars   and   Huber   banjos      

> 从上面的结果来看，这三个类在单线程程序中的性能差别一目了然
>
> 采用String对象时，即使运行次数仅是采用其他对象的1/100，其执行时间仍然比其他对象高出25倍以上；
>
> 而采用StringBuffer对象和采用StringBuilder对象的差别也比较明显，前者是后者的1.5倍左右。
>
> 由此可见，如果我们的程序是在单线程下运行，或者是不必考虑到线程同步问题，我们应该优先使用StringBuilder类；
>
> 当然，如果**要保证线程安全，自然非StringBuffer莫属**了。  
> **除了对多线程的支持不一样外，这两个类的使用几乎没有任何差别**，上面的例子就是个很好的说明。
>
> **appendItemsToStringBuiler**和**appendItemsToStirngBuffer**两个方法除了采用的对象分别为StringBuilder和StringBuffer外，其他完全相同，而效果也完全相同。