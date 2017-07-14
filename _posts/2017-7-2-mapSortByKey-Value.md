---
layout: post
title: Java Map按值排序
date: 2017-7-2 11:00
categories: Java
tags: [Java]
---

* content
{:toc}
> 有很多情况下需要我们根据键值对里面的值进行排序，而不是根据键进行排序。这里面的核心思路是写一个比较器，然后利用Collections里面的sort方法，传一个list和一个比较器进去。

## Map介绍

Map在日常开发中是使用的非常频繁的一个集合接口，它的实现类主要有：HashMap、TreeMap、HashTable以及LinkedHashMap等

## 两种最常用的Map实现类

### 1、TreeMap

TreeMap 基于红黑树的NavigableMap实现。该映射根据其键的自然顺序进行排序，或者创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。

### 2、HashMap

HashMap的值是没有顺序的，它是按照key的HashCode来实现的，对于这个无序的HashMap我们要怎么来实现排序呢？参照TreeMap的value排序

注意：Map.Entry返回Collections视图。

## 按key排序

### TreeMap默认按照key升序进行排序

```java
public static void treeMapSortByKeyASC() {
        // 默认情况，TreeMap按key升序排序
        Map<String, Integer> map = new TreeMap<>();
        map.put("acb1", 5);
        map.put("bac1", 3);
        map.put("bca1", 20);
        map.put("cab1", 80);
        map.put("cba1", 1);
        map.put("abc1", 10);
        map.put("abc2", 12);

        // 默认情况下，TreeMap对key进行升序排序
        System.out.println("------------正常情况，TreeMap按key升序排序--------------------");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + ":" + entry.getValue());
        }
    }
```

运行结果：

> ------------正常情况，TreeMap按key升序排序--------------------
> abc1:10
> abc2:12
> acb1:5
> bac1:3
> bca1:20
> cab1:80
> cba1:1
>
> Process finished with exit code 0

### TreeMap默认按照key降序进行排序

> 这里的重点是要自己写一个比较器 Comparator

```java
public static void treeMapSortByKeyDESC(){
        //TreeMap按key降序排序
        Comparator<String> keydesc = new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o2.compareTo(o1);
            }
        };
        Map<String,Integer>  map = new TreeMap<>(keydesc);
        map.put("acb1", 5);
        map.put("bac1", 3);
        map.put("bca1", 20);
        map.put("cab1", 80);
        map.put("cba1", 1);
        map.put("abc1", 10);
        map.put("abc2", 12);
        //按照key降序排序
        for (Map.Entry<String,Integer> entry:
            map.entrySet() ) {
            System.out.println(entry.getKey()+":"+entry.getValue());
        }
    }
```

## 按value排序

### 按照value升序

核心的比较器（Java 8的写法）：

> ```
> Comparator<Map.Entry<String, Integer>> valueComparator = Comparator.comparingInt(Map.Entry::getValue);
> ```

核心的比较器（之前的写法）：

> ```
> Comparator<Map.Entry<String, Integer>> valueComparator = new Comparator<Map.Entry<String,Integer>>() {
>         @Override
>         public int compare(Entry<String, Integer> o1,
>                 Entry<String, Integer> o2) {
>             // TODO Auto-generated method stub
>             return o1.getValue()-o2.getValue();
>         }
>     };
> ```

步骤：

1、构造一个TreeMap，输入值  

2、写比较器  

3、将Map转换为list（因为我们利用的是Collections的排序）  

4、排序。Collections.sort(list,比较器)  

5、输出  

```java
public static void valueUpSort() {
        // 默认情况，TreeMap按key升序排序
        Map<String, Integer> map = new TreeMap<>();
        map.put("acb1", 5);
        map.put("bac1", 3);
        map.put("bca1", 20);
        map.put("cab1", 80);
        map.put("cba1", 1);
        map.put("abc1", 10);
        map.put("abc2", 12);
        // 升序比较器
        Comparator<Map.Entry<String, Integer>> valueComparator = Comparator.comparingInt(Map.Entry::getValue);
        // map转换成list进行排序
        List<Map.Entry<String, Integer>> list = new ArrayList<>(map.entrySet());

        // 排序
        Collections.sort(list, valueComparator);

        // 默认情况下，TreeMap对key进行升序排序
        System.out.println("------------map按照value升序排序--------------------");
        for (Map.Entry<String, Integer> entry : list) {
            System.out.println(entry.getKey() + ":" + entry.getValue());
        }
    }
```

运行结果：  

> ------------map按照value升序排序--------------------
> cba1:1
> bac1:3
> acb1:5
> abc1:10
> abc2:12
> bca1:20
> cab1:80
>
> Process finished with exit code 0

### 按照value降序

核心的比较器（Java 8 的写法）：  

> ```
> Comparator<Map.Entry<String, Integer>> valueComparatordesc = (o1, o2) -> o2.getValue()-o1.getValue();
> ```

代码：  

```java
 public static void valueDownSort() {
        // 默认情况，TreeMap按key降序排序
        Map<String, Integer> map = new TreeMap<>();
        map.put("acb1", 5);
        map.put("bac1", 3);
        map.put("bca1", 20);
        map.put("cab1", 80);
        map.put("cba1", 1);
        map.put("abc1", 10);
        map.put("abc2", 12);
        // 升序比较器
        Comparator<Map.Entry<String, Integer>> valueComparatordesc = (o1, o2) -> o2.getValue()-o1.getValue();
        // map转换成list进行排序
        List<Map.Entry<String, Integer>> list = new ArrayList<>(map.entrySet());

        // 排序
        Collections.sort(list, valueComparatordesc);

        // 默认情况下，TreeMap对key进行降序排序
        System.out.println("------------map按照value降序序排序--------------------");
        for (Map.Entry<String, Integer> entry : list) {
            System.out.println(entry.getKey() + ":" + entry.getValue());
        }
    }
```



运行结果：  

>------------map按照value降序序排序--------------------
>cab1:80
>bca1:20
>abc2:12
>abc1:10
>acb1:5
>bac1:3
>cba1:1
>
>Process finished with exit code 0

参考博客： https://crane-yuan.github.io/2016/08/15/The-map-of-java-sorted-by-value/