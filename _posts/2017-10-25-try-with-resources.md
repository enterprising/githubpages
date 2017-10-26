---
layout: post
title: Java7的 try-with-resources
date: 2017-10-25 17:00
categories: Java
tags: [Java]
---

* content
{:toc} 
# try-with-resources概述

1. Java7的一个新特性
2. 减少内存泄漏的情况发生
3. 无需显式关闭资源，只要你把资源申请的语句包在try后面的括号里面就OK
4. 要用的话，那要实现AutoCloseable接口

# 用法

```java
try(BufferedReader br = new BufferedReader(new FileReader(path));) {
  return br.readLine();
}
```

# 对比

在Java 7之前，IO操作为了保证资源能被关闭，往往会在try代码块后加上finally代码块来处理资源的关闭。

```
BufferedReader br = new BufferedReader(new FileReader(path));
try {
    return br.readLine();
} finally {
    if (br != null) br.close();
}
```

Java 7新增了try-with-resources语法来保证资源的关闭

```
try(BufferedReader br = new BufferedReader(new FileReader(path));) {
  return br.readLine();
}
```

BufferedReader 会在代码块里处理完后会被关闭，不需要显式调用BufferedReader的close方法。

# 关闭资源与catch-fnally执行顺序

try-with-resources也可以添加catch和finally语句，但catch和finally里的代码在资源被关闭后才会被执行。

```
public void testTryWithResources2() throws IOException {
    try (ClosableResource resource = new ClosableResource()) {
        throw new RuntimeException("try exception");
    } catch (Exception e) {
        System.out.println("catch exception:" + e.getMessage());
        System.out.println("suppressed exception:" + e.getSuppressed()[0].getMessage());
    } finally {
        System.out.println("finally exception");
    }
}

class ClosableResource implements AutoCloseable{

    @Override
    public void close() throws Exception {
        System.out.println("close resource");
        throw new RuntimeException("close resource runtime exception");
    }
}
```

输出的顺序是

```
close resource
catch exception:try exception
suppressed exception:close resource runtime exception
finally exception
```

输出顺序是关闭资源，catch捕获try里抛出的异常，最后才是执行finally代码。其中关闭资源抛出的异常可以使用Throwable.getSuppressed()获得。

# 参考博客

1. [Java try-with-resources用法](https://majing.io/questions/778)
2. [Java 7中的Try-with-resources](http://ifeve.com/java-7%E4%B8%AD%E7%9A%84try-with-resources/)