---
layout: post
title: 深入理解Java异常机制
date: 2017-8-8 20:00
categories: Java
tags: [Java,异常]
---

* content
{:toc} 
# 框架

![java异常](https://ws1.sinaimg.cn/large/006tNc79gy1fickof1zxyj30go0ahab1.jpg)

<br/>

# Throwable

​	是Java中所有错误或异常的父类，下面有Error和Exception。throw的意思的可抛出的。

​	The Throwable class is the superclass of all errors and exceptions in the Java language.  - - 官方API文档

<br/>

# Error

​	Throwable的子类，用于标记非常严重的错误，在程序中不应该去try和catch这种错误，因为绝大多数的错误都是非正常的，也就是说根本不该出现。

<br/>

# Exception

​	Throwable的子类，用于指示一种合理的程序想去 catch 的条件。即它仅仅是一种程序运行条件，而非严重错误，并且鼓励用户程序去 catch 它。

<br/>

# 运行时异常和检查时异常

![异常的架构](http://uploadfiles.nowcoder.com/images/20151010/214250_1444467985224_6A144C1382BBEF1BE30E9B91BC2973C8)

1. 粉红色的是受检查的异常(checked exceptions),其**必须被try{}catch语句块所捕获**,或者在方法签名里通过throws子句声明.受检查的异常必须在编译时被捕捉处理,命名为 CheckedException 是因为Java编译器要进行检查,Java虚拟机也要进行检查,以确保这个规则得到遵守. 
2. 绿色的异常是运行时异常(runtime exceptions),**需要程序员自己分析代码决定是否捕获和处理**,比如空指针,被0除... 
3. 而声明为Error的，则属于严重错误，如系统崩溃、虚拟机错误、动态链接失败等，这些错误无法恢复或者不可能捕捉，将导致应用程序中断，Error不需要捕捉。

<br/>

# Error和Exception的区别

​	error 表示系统级的错误和程序不必处理的异常，是恢复不是不可能但很困难的情况下的一种严重问题；比如内存溢出，不可能指望程序能处理这样的情况；

​	 exception 表示需要捕捉或者需要程序进行处理的异常，是一种设计或实现问题；也就是说，它表示如果程序运行正常，从不会发生的情况。

<br/>

# throw和throws的区别

尽管他们看起来相似，都是在处理异常时候使用到的。但在代码里的使用方法和用到的地方是不同的。**throws总是出现在一个函数头中**，用来标明该成员函数可能抛出的各种异常, 你也可以申明未检查的异常，但这不是编译器强制的。如果方法抛出了异常那么调用这个方法的时候就需要将这个异常处理。另一个关键字  **throw 是用来抛出任意异常的，按照语法你可以抛出任意 Throwable (i.e. Throwable 或任何Throwable的衍生类) , throw可以中断程序运行，因此可以用来代替return .** 最常见的例子是用 throw 在一个空方法中需要return的地方抛出 UnSupportedOperationException 代码如下 :

```
private static void show() {   
     throw new UnsupportedOperationException("Not yet implemented");
 } 
```

<br/>

# 故事

pom.xml少了dependency依赖
编译器能编译通过，并且发现不了错误

解决步骤
1、jstack

![屏幕快照 2017-08-08 下午3.22.08](https://ws4.sinaimg.cn/large/006tNc79gy1fickrxrexsj30ho06kdgl.jpg)

![屏幕快照 2017-08-08 下午3.22.22](https://ws3.sinaimg.cn/large/006tNc79gy1ficksgt0f0j31d811oh1s.jpg)


居然没发现错误？？

2、catch异常

```java
try {
    slack("```" + sb.toString() + "```");
} catch (Exception e) {
    e.printStackTrace();
} 
```

还是没反应

3、catch Throwable

```java
try {
    slack("```" + sb.toString() + "```");
} catch (Exception e) {
    e.printStackTrace();
} catch (Throwable t){
    t.printStackTrace();
}
```

抓到了

![屏幕快照 2017-08-08 下午3.26.37](https://ws1.sinaimg.cn/large/006tNc79gy1fickt4opzsj30hv08gmz7.jpg)

其实可以直接catch Error

```java
try {
  slack("" + sb.toString() + "");
} catch (Exception e) {
  e.printStackTrace();
} catch (Error t){
  t.printStackTrace();
}
```

![屏幕快照 2017-08-08 下午3.49.31](https://ws2.sinaimg.cn/large/006tNc79gy1ficktv2n0uj30ot089mzs.jpg)

关于Throwable：
The Throwable class is the superclass of all errors and exceptions in the Java language. 

<br/>

# 参考资料

1、异常处理。<http://wiki.jikexueyuan.com/project/java-interview-bible/exception.html>

2、 链接：[https://www.nowcoder.com/questionTerminal/a4a96e226c7b4dafae8250db4ff88af5?source=relative](https://www.nowcoder.com/questionTerminal/a4a96e226c7b4dafae8250db4ff88af5?source=relative)