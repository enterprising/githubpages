---
layout: post
title: Java反射与类型擦除
date: 2017-8-23 17:00
categories: Java
tags: [Java]
---

* content
{:toc} 
# 类型擦除

根据我之前研究《Java编程思想》的理解，类型擦除一般跟泛型练习在一起，这货的意思大概就是指：虽然一个泛型集合，比如ArrayList\<Integer\>，你一看就知道里面放的是Integer类型的，但是在JVM层，它才不管这个，底层看到的全是Object。编译器在编译的时候去掉类型，这个过程大概就叫类型擦除。

在程序编译后的 .class 文件是没有泛型约束的，这个现象就叫做类型擦除。

# 反射

反射的意思大概是指，**在运行时**知道类的所有信息和变量类型。有了这个我们就不用再编译时知道对象的类型了（RTTI实现这个）

在运行时可以通过反射机制来获取到被擦除的类型信息。

因为 .class 没有类型的约束，那么，我们通过反射就可以向里面添加任意类型的元素。

# demo

向的ArrayList\<Integer\>集合中添加一个字符串数据。

```java
public static void main(String[] args) throws ReflectiveOperationException {
  // 新建一个ArrayList<Integer>集合，并添加元素
  ArrayList<Integer> array = new ArrayList<>();
  array.add(10);
  array.add(20);
  array.add(30);
  array.add(40);
  // array.add("因为泛型的原因，字符串添加不进去");
  // 获取ArrayList<>的Class对象
  Class<? extends ArrayList> arrayClass = array.getClass();
  // 找到add方法
  Method addMethod = arrayClass.getMethod("add", Object.class);
  addMethod.invoke(array, "好厉害");
  System.out.println(array);
}
```

运行效果：

> [10, 20, 30, 40, 好厉害]
>
> Process finished with exit code 0