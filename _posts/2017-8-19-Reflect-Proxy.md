---
layout: post
title: Java 反射和动态代理
date: 2017-8-19 16:00
categories: Java
tags: [Java]
---

* content
{:toc} 
本文内容主要整理自《Java编程思想》第四版、第14章 类型信息。

# 介绍

要弄清楚反射，就必须了解JVM级别的东西。RTTI和反射需要一起看。

Java是如何让我们**在运行时识别对象和类的信息**？（或者说Java里的class文件的加载。）

有两种方式：

一种是通过传统的 RTTI，它假定我们在编译时已经知道了自己的所有类型；

还有一种就是 反射，它允许我们在运行时发现和使用类的信息。

上面这段是书上的原话.. 有点晦涩难懂。

换种说法就是，RTTI就是类型是编译器一直的，这种文件的.class文件在编译的时候，编译器会把.cladd文件打开检查（注意，不是加载）；

反射就是我们可能从别的地方获取到了一个引用，然后动态的把这个未知类型引用的对象的.class文件加载到JVM里面。

# RTTI

Run- Time Type Identification 运行时类型识别。

RTTI最大的限制就是，这个类型在编译时必须已知，这样才能使用RTTI来识别它。

# 反射

反射算是Java核心技术的一种存在了.. 惭愧，了解甚微。

主要在Java.reflect这个包里面，里面有Method,Constructor,field,Proxy,InvocationHandler 等类。

反射最大的作用就是：我们可以**不用在编译时知道对象的类型，而在运行时得到。**

例如，我们可以只通过类的名字，就能直到它内部方法和字段等。

看上去好像没啥用，但假设你获取了一个指向某个并不在你程序空间中的对象的引用，那么RTTI根本识别不了.. 嗯，你就不知道对象的类型和信息了。但反射可以做到。

其实，反射机制并没有特别神奇。当通过反射和一个未知类型的对象打交道时JVM只是简单地检这个对象，看它属于哪个特定的类（就行RTTI那样）。在用它做其它事情之前必须先加载那个类的Class对象。因此，这个类的.class文件对于JVM来说必须是可获取的：要么在本地机器，要么可以通过网络取得。

所以，RTTI和反射之间的真正区别只在于：

- 对于RTTI来说，编译器在编译时打开和检查 .classs 文件。换句话说就是，我们可以用“普通”方式调用对象的所有方法。
- 对于反射机制来说，.class文件在编译时是不可获取的，所以在运行时打开和检查 .class 文件。

## 类方法提取器

通常是不需要直接使用反射工具的.. 嗯，难怪不常见。

在你创建更加动态的代码的时候，反射会很有用。反射在Java中是用来支持其它特性的，例如对象序列化和javaBean。

当需要动态地提取某个类的信息时，可以使用类方法提取器：**Class.forName()**

<br/>

# 动态代理

.. 这个的话，你就需要先了解设计模式里面的代理模式了。

代理模式主要是指，在操作者和被操作者之间加了一层代理层，代理通常充当着中间人的角色。

##静态代理：

```java
interface Interface {
    void doSomething();

    void somethingElse(String arg);
}

class RealObject implements Interface {
    public void doSomething() {
        print("doSomething");
    }

    public void somethingElse(String arg) {
        print("somethingElse " + arg);
    }
}

class SimpleProxy implements Interface {
    private Interface proxied;

    public SimpleProxy(Interface proxied) {
        this.proxied = proxied;
    }

    public void doSomething() {
        print("SimpleProxy doSomething");
        proxied.doSomething();
    }

    public void somethingElse(String arg) {
        print("SimpleProxy somethingElse " + arg);
        proxied.somethingElse(arg);
    }
}

class SimpleProxyDemo {
    public static void consumer(Interface iface) {
        iface.doSomething();
        iface.somethingElse("bonobo");
    }

    public static void main(String[] args) {
        // 普通模式
        consumer(new RealObject());
        // 代理模式
        consumer(new SimpleProxy(new RealObject()));
    }
}
```

运行结果：

> doSomething  
> somethingElse bonobo
>
> 上面两条是没用代理模式输出的，下面是代理模式输出的。
>
> SimpleProxy doSomething  
> doSomething  
> SimpleProxy somethingElse bonobo  
> somethingElse bonobo

## 动态代理

是升级版的代理模式，它可以**动态地创建代理并动态地处理对所代理方法的调用**。

这里还是需要调用上面的Interface接口和RealObject类。

```java
import java.lang.reflect.*;

class DynamicProxyHandler implements InvocationHandler {
    private Object proxied;

    public DynamicProxyHandler(Object proxied) {
        this.proxied = proxied;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("**** proxy: " + proxy.getClass() +
                ", method: " + method + ", args: " + args);
        if (args != null)
            for (Object arg : args)
                System.out.println("  " + arg);
        return method.invoke(proxied, args);
    }
}

class SimpleDynamicProxy {
    public static void consumer(Interface iface) {
        iface.doSomething();
        iface.somethingElse("bonobo");
    }

    public static void main(String[] args) {
        RealObject real = new RealObject();
        consumer(real);
        // Insert a proxy and call again:
        Interface proxy = (Interface) Proxy.newProxyInstance(
                Interface.class.getClassLoader(),
                new Class[]{Interface.class},
                new DynamicProxyHandler(real));
        consumer(proxy);
    }
} /* Output: (95% match)	
doSomething
somethingElse bonobo
**** proxy: class $Proxy0, method: public abstract void Interface.doSomething(), args: null
doSomething
**** proxy: class $Proxy0, method: public abstract void Interface.somethingElse(java.lang.String), args: [Ljava.lang.Object;@42e816
  bonobo
somethingElse bonobo
*///:~
```

说明：

通过调用静态方法Proxy.newProxyInstance()可以创建动态代理，它需要三个参数：1. 类加载器（一般是从已经被加载的对象中获取其类加载器，然后传给它）、2.一个你希望该代理实现的接口列表、3.InvocationHandler的一个实现（这个接口是反射包里面的一个东西）。

动态代理可以将所有调用重定向到 调用处理器，以防你需要区分请求的来源。

通常你会执行被代理的操作，然后使用invoke()方法，将请求按需要转发给被代理对象，并传入必须的参数：

```java
import java.lang.reflect.*;

import static net.mindview.util.Print.*;

class MethodSelector implements InvocationHandler {
    private Object proxied;

    public MethodSelector(Object proxied) {
        this.proxied = proxied;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (method.getName().equals("interesting"))
            print("Proxy detected the interesting method");
        return method.invoke(proxied, args);
    }
}

interface SomeMethods {
    void boring1();

    void boring2();

    void interesting(String arg);

    void boring3();
}

class Implementation implements SomeMethods {
    public void boring1() {
        print("boring1");
    }

    public void boring2() {
        print("boring2");
    }

    public void interesting(String arg) {
        print("interesting " + arg);
    }

    public void boring3() {
        print("boring3");
    }
}

class SelectingMethods {
    public static void main(String[] args) {
        SomeMethods proxy = (SomeMethods) Proxy.newProxyInstance(
                SomeMethods.class.getClassLoader(),
                new Class[]{SomeMethods.class},
                new MethodSelector(new Implementation()));
        proxy.boring1();
        proxy.boring2();
        proxy.interesting("bonobo");
        proxy.boring3();
    }
} /* Output:
boring1
boring2
Proxy detected the interesting method
interesting bonobo
boring3
*///:~
```

<br/>

# 参考资料

1、《Java编程思想》第四版

2、[Java动态代理与反射详解](http://www.cnblogs.com/haodawang/p/5967185.html)

3、[Java反射和动态代理详解和实例](http://blog.csdn.net/he90227/article/details/39155613)