---
layout: post
title: Java8 Map的compute()方法
date: 2017-7-13 15:00
categories: Java
tags: [Java]
---

### 三个重要的关键字

**compute :计算，Absent：不存在，Present：不存在**

```java
Map<Integer, String> map = new HashMap<>();
map.put(1, "a");
map.put(2, "b");
map.put(3, "c”);
```

以下操作建立在上面这个Map集合的基础之上，

### 1、computeIfAbsent()  

 例如：map.computeIfAbsent(4, key -> key + "af”);

 第一个参数是key，第二个参数是lambda函数

目的是：如果这个key不存在，那么我们就 存一下，并且value = key+”af”

运行结果：System.out.println(map.get(4));  //输出 4af

如果你不想使用key进行value的计算，可以直接使用

map.putIfAbsent(4, "af”);

输出就为： af



### 2、map.computeIfPresent()

例如：map.computeIfPresent(1, (key, value) -> value+"asd”);    //这个就比较重要了。如果存在就对齐进行修改。

当设置的参数为 null 的时候，代表删除该元素



### 3、compute() 

该方法是上面两个方法的集合体。



### 4、举个例子

目的：遍历一个list，如果list里面的对象的name相同，就取出来叠加，否则直接加进去。

Map\<String, Integer> groupmap = new HashMap<>();

正常的写法：

```java
for (Group group : list) {
    if (groupmap.keySet().contains(group.getGroupName())) {
        int groupload = groupmap.get(group.getGroupName());
        groupmap.remove(group.getGroupName());
        groupmap.put(group.getGroupName(), groupload + group.getLoad());
    } else {
        groupmap.put(group.getGroupName(), group.getLoad());
    }
}
```

新写法：

```java
for (Group group : list) {
  groupmap.computeIfPresent(group.getGroupName(), (String key, Integer value) -> value + group.getLoad());
  groupmap.putIfAbsent(group.getGroupName(), group.getLoad());
}
```



详细说明：

## computeIfAbsent 方法

如果指定的key不存在，则通过指定的K -> V计算出新的值设置为key的值，类似代码如下：

```java
if (map.get(key) == null) {
    V newValue = mappingFunction.apply(key);
    if (newValue != null)
        map.put(key, newValue);
}
```

示例代码如下：

```java
map.computeIfAbsent(1, key -> key + " computed");
// 存在key为1，则不进行计算，输出值 a
System.out.println(map.get(1));

map.computeIfAbsent(4, key -> key + " computed");
// 不存在key为4，则进行计算，输出值 4 computed
System.out.println(map.get(4));
```

## computeIfPresent 方法

如果指定的key存在，则根据旧的key和value计算新的值newValue, 如果newValue不为null，则设置key新的值为newValue, 如果newValue为null, 则删除该key的值，类似代码如下：

```java
if (map.get(key) != null) {
    V oldValue = map.get(key);
    V newValue = remappingFunction.apply(key, oldValue);
    if (newValue != null)
        map.put(key, newValue);
    else
        map.remove(key);
}
```

示例代码如下：

```java
map.computeIfPresent(1, (key, value) -> (key + 1) + value);
// 存在key为1， 则根据旧的key和value计算新的值，输出 2a
System.out.println(map.get(1));

map.computeIfPresent(2, (key, value) -> null);
// 存在key为2， 根据旧的key和value计算得到null，删除该值，输出 null
System.out.println(map.get(2));
```

## compute 方法

compute方法是computeIfAbsent与computeIfPresent的综合体。