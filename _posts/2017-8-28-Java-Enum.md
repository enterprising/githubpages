---
##layout: post
title: Java 枚举类型
date: 2017-8-29 21:00
categories: Java
tags: [Java]
---

* content
{:toc} 
内容整理自《Java编程思想》第四版、第19章 枚举类型

关于枚举类：数目固定，类型和值不会变的情况一般用枚举类。就比如星期一 ~ 星期天这种。

# 基本enum特性

调用enum的values()方法，可以遍历enum实例。这个方法返回enum实例的数组，并且顺序是严格按照声明时候的顺序。

可以用 == 来比较枚举实例，编译器会自动为你提供equals()方法和hashCode()方法。

Enum还实现了Comparable接口，所以具有compareTo()方法，同时，它还实现了Serializable接口。

# 向enum中添加新方法

除了不能被继承之外，基本上可以将enum看做一个常规的类。也就是说，我们可以向enum中添加方法，连main方法都可以有。

## 覆盖enum的方法

主要就是覆盖里面的toString()方法，然后按照我们想要的实现来输出结果。

# switch语句中的enum

一般情况下switch的case里面是整数类型（Java7之后支持String类型），所以说在switch中使用enum，是enum提供的一项非常便利的功能。

# values()的神秘之处

