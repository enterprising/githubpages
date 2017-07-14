---
layout: post
title: Java集合类总结
date: 2017-7-9 10:00
categories: Java
tags: [Java]
---

* content
{:toc}
## 1、数组 将数字和对象联系起来。

​    它保存类型明确的对象，查询对象时不需要对结果做类型转换。

​    它可以是多维的，可以保存基本类型的数据。

​    但是，数组一旦从生成，其容量就不能改变。

## 2、Collection 保存单一的元素，而Map保存相关联的键值对。

​    各种Collection和各种Map都可以在你向其中添加更多元素时自动调整尺寸。

​    容器不能持有基本类型，但有包装类，比如 int 对应的 Integer 等

## 3、关于泛型

​      有了Java的泛型，你就可以指定容器中存放的对象类型，

## 4、List与数组的区别

像数组一样，List也建立数字索引与对象的关联，因此数组和List都是排好序的容器。不同的地方在于List可以自动扩充容量，而数组不可以。

## 5、ArrayList 和 LinkedList

如果要进行大量的随机访问，就使用ArrayList。  如果要经常从表中间插入或删除元素，则应该使用LinkedList。

## 6、各种队列和栈的行为，由LinkedList提供支持。

###    Stack 栈

   ```java
 public class Stack<T>{   
        private LinkedList<T> storage = new LinkedList<T>();
        public void push<T v>{};
        public T peek() {};  //输出栈顶，但不弹出
        public T pop() {};  //移除并返回栈顶元素
                   empty(){}
                   toString(){};
    }
   ```



​    窄化了LinkedList，不需要继承，只需要实现里面的部分功能。

###  Queue 队列

​     最重要的是 先进先出

​    细节： LinkedList 提供了方法以支持队列的行为，并且实现了Queue接口。因此LinkedList可以用作Queue的一种实现。

​    常用方法说明：

```java
	offer( )  将一个元素插入到队列的尾部
    peek( ) 和 element( ) 都在不移除的情况下返回队列头
        区别在于： peek( )方法在队列为空时返回 null ，而element会抛异常
    poll( )和remove( )方法将移除并返回队列头，
        区别在于：poll( )在队列为空时返回null，而remove( )会抛出异常
```





## 7、Map是一种将对象（而非数字）与对象相关联的设计。 

​    HashMap设计用来快速访问，而TreeMap保持 “键” 始终处于排序状态。  所以HashMap要快一点

​    LinkedHashMap保持元素插入的顺序，但是也通过散列提供了快速访问的能力。

## 8、Set不接受重复元素。HashSet提供最快的查询速度，而TreeSet保持元素处于排序状态。

​    LinkedHashSet以插入顺序保存元素

## 9、新程序中不可以使用过时的 Vector、HashTable 和 Stack

## 10、附：常用集合类的 线程安全性

| 集合类                 | Key          | Value        | Super       | 说明       |
| ------------------- | ------------ | ------------ | ----------- | -------- |
| **Hashtable**       | 不允许为 null    | 不允许为 null    | Dictionary  | **线程安全** |
| `ConcurrentHashMap` | 不允许为 null    | 不允许为 null    | AbstractMap | 分段锁技术    |
| TreeMap             | 不允许为 null    | 允许为 null     | AbstractMap | 线程不安全    |
| HashMap             | **允许为 null** | **允许为 null** | AbstractMap | 线程不安全    |

以上内容摘自《Java编程思想》和《Java开发手册》