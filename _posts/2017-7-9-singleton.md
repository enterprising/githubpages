---
layout: post
title: 设计模式-创建型-单例模式
date: 2017-7-9 14:00
categories: 设计模式
tags: [设计模式]
---

* content
{:toc}
单例模式是所有的设计模式中用的最多的一个，也是事项方式非常多的一种设计模式。在面试中，这个很考验基础。

## 为什么会有单例模式：

在很多情况下，有些类只需要一个实例就可以了。比如 工具类、线程池、数据库、配置文件等。

## 作用：

保证在整个应用程序的生命周期中，任何一个时刻，单例类的实例都只存在一个。

## 全局变量和单例模式的区别：

1、全局变量不能保证应用程序中只有一个实例

2、编码规范中规定少用全局变量

3、全局变量不能实现继承

## 几种实现方式：

### 饿汉式【可用】

* 优点：实现简单，在类加载的时候就完成了实例化，避免了线程同步的问题
* 缺点：类加载的时候就实例化了，所以没有达到懒加载的效果。要是没用的话，就造成线程的浪费了

```java
/**
 * Created by peng.tan on 17/7/9.
 * 这是最简单，最容易想到的一种实现方式   【可用】
 * 俗名又叫单例模式的饿汉式
 */
public class Singleton_1 {
    // 构造方法
    private Singleton_1() {
    }

    private static Singleton_1 uniqueInstance = new Singleton_1();

    public static Singleton_1 getInstance() {
        return uniqueInstance;
    }
}
```

### 懒汉式 【线程不安全，不可用】

​	之所以会叫懒汉式，是因为当使用的时候才new一个对象，实现了懒加载

* 为什么线程不安全？

  假设有两个线程，线程1、线程2 。当线程1跑到if(uniqueInstance==null)里面但却没有执行new Singleton_2的时候，uniqueInstance还是null，这时候线程2可能也进来了。这时候可能就创建了两个Singleton__2的对象。

  所以，这种方式不可用

```java
/**
 * Created by peng.tan on 17/7/9.
 * 单例模式的懒汉式写法       【线程不安全，不可用】
 */
public class Singleton_2 {
    private Singleton_2() {

    }

    private static Singleton_2 uniqueInstance = null;

    public static Singleton_2 getInstance() {
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton_2();
        }
        return uniqueInstance;
    }
}
```

### 懒汉式加锁 【线程安全，但效率低，最好不用】

效率太低了，每个线程在想获得类的实例时候，执行getInstance()方法都要进行同步。而其实这个方法只执行一次实例化代码就够了，后面的想获得该类实例，直接return就行了。方法进行同步效率太低要改进。

```java
/**
 * Created by peng.tan on 17/7/9.
 * 懒汉式的 加锁。 【线程安全，但效率极低】
 * 缺点：加锁的效率太低
 */
public class Singleton_3 {
    private Singleton_3() {
    }

    private static Singleton_3 uniqueInstance = null;

    public static synchronized Singleton_3 getInstance() {
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton_3();
        }
        return uniqueInstance;
    }
}
```

### 懒汉式加锁 【线程不安全，不用】

至于这种写法为什么不安全，其实原理和第2种差不多。最后会创建多个Singleton对象

```java
/**
 * Created by peng.tan on 17/7/9.
 * 懒汉式加锁 --不安全写法 【不可用】
 */
public class Singleton_4 {
    private Singleton_4() {
    }

    private static Singleton_4 uniqueInstance = null;

    public static Singleton_4 getInstance() {
        if (uniqueInstance == null) {
            synchronized(Singleton_4.class){
              uniqueInstance = new Singleton_4();
            }
        }
        return uniqueInstance;
    }
}
```

### 懒汉式双重加锁 【线程安全，推荐用】

​	根据前面几种改进的，要看懂这个，必须先看懂前面三个。

```java
/**
 * Created by peng.tan on 17/7/9.
 * 懒汉式的双重加锁 【线程安全， 推荐用】
 * 保证了延迟加载和线程安全
 */
public class Singleton_5 {

    private Singleton_5() {
    }

    private volatile static Singleton_5 uniqueInstance = null;

    public static synchronized Singleton_5 getInstance() {
        if (uniqueInstance == null) {
            synchronized (Singleton_5.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton_5();
                }
            }
        }
        return uniqueInstance;
    }
}
```

### 内部类 【线程安全，建议用】

​	和第一种饿汉式类似，但是又比它高级了很多。

​	1、采用静态内部类，在该类被加载的时候并没有实现实例化。所以就达到了 **懒加载** 的目的

​	2、类的静态属性只会在第一次加载类的时候初始化，所以在这里，JVM帮助我们保证了线程的安全性，在类			进行初始化时，别的线程是无法进入的。

​	优点：避免了线程不安全，延迟加载，效率高。

```java
/**
 * Created by peng.tan on 17/7/9.
 * 单例模式 的 内部类实现 【这里是 JVM帮我们实现线程安全，建议用】
 */
public class Singleton_6 {
    private Singleton_6() {
    }
    
    private static class SingletonHolder {
        private static Singleton_6 instance = new Singleton_6();
    }

    public static Singleton_6 getInstance() {
        return SingletonHolder.instance;
    }
}
```

### 枚举实现 【极其推荐】

​	借助JDK1.5中添加的枚举来实现单例模式。不仅能避免多线程同步问题，而且还能防止反序列化重新创建新的对象。

```java
/**
 * Created by peng.tan on 17/7/9.
 * 单例模式 枚举 实现。 这是高级玩法。  【及其建议用】
 * 外部访问方式：Singleton_Enum.instance.method(); 
 */
public enum Singleton_Enum {
    instatnce;

    private Singleton_Enum() {
    }

    public void method() {
    }
}
```



参考博客：

<http://blog.csdn.net/dmk877/article/details/50311791>