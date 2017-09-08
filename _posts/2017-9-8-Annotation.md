---
layout: post
title: Java注解
date: 2017-9-8 9:00
categories: Java
tags: [Java]
---

* content
{:toc} 
# 推荐博客

[秒懂，Java 注解 （Annotation）你可以这样学](https://zhuanlan.zhihu.com/p/27643133?utm_source=wechat_session&utm_medium=social) 作者：frank909

# 简单说明

注解可以理解成生活中的标签。

注解主要针对的是编译器和其它工具软件。

注解有许多用处，主要如下： - 提供信息给编译器： 编译器可以利用注解来探测错误和警告信息 - 编译阶段时的处理： 软件工具可以用来利用注解信息来生成代码、Html文档或者做其它相应处理。 - 运行时的处理： 某些注解可以在程序运行的时候接受代码的提取

注解可以理解成一个标签

注解：@FunctionalInterface

# 函数式接口

其实就是一个具有一个方法的普通接口

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```

函数式接口注解的作用：

所有函数式接口注解声明的接口，都可以很容易地转换成lambda表达式。

# 注意

注解仅仅是元数据，和业务逻辑无关，所以当你查看注解类时，会发现里面没有任何逻辑处理。

javadoc中的@author、@version、@param、@return、@deprecated、@hide、@throws、@exception、@see是标记，并不是注解；

Java 5引入的。

注解为我们在代码中添加信息提供了一种形式化的方法，使我们可以在稍后某个时刻非常方便地使用这些数据。

注解和接口一样，都会被编译成 .class 文件

# 总结

1. 如果注解难于理解，你就把它类同于标签，标签为了解释事物，注解为了解释代码。
2. 注解的基本语法，创建如同接口，但是多了个 @ 符号。
3. 注解的元注解。
4. 注解的属性。
5. 注解主要给编译器及工具类型的软件用的。
6. 注解的提取需要借助于 Java 的反射技术，反射比较慢，所以注解使用时也需要谨慎计较时间成本。

作用：

1. 格式检查：告诉编译器信息，比如被@override标记的方法如果不是父类的某个方法，IDE会报错；
2. 减少配置：运行时动态代理，得到注解信息，实现代替配置文件的功能；
3. 减少重复工作：比如？JUnit的@Test，真的十分方便。