---
layout: post
title: 根据Map的value分组并统计个数
date: 2017-7-19 10:00
categories: Java
tags: [Java]
---

* content
{:toc}
## 程序目的

根据map里面的value分组，并且统计每组的元素个数

## 数据结构

```java
/**
 * 用于将machine分类
 *
 * @return
 */
public static Map<String, Integer> initMachineGroup() {
    Map<String, Integer> machineGroups = new HashMap<>();

    machineGroups.put("xg-dal-c4-23", 2);
    machineGroups.put("xg-dal-c4-24", 2);
    machineGroups.put("xg-dal-c4-26", 2);

    machineGroups.put("xg-dal-c6-7", 3);
    machineGroups.put("xg-dal-c6-8", 3);
    machineGroups.put("xg-dal-c6-9", 3);
    machineGroups.put("xg-dal-c6-10", 3);

    machineGroups.put("xg-dal-c6-11", 4);
    machineGroups.put("xg-dal-c6-12", 4);
    machineGroups.put("xg-dal-c6-13", 4);
    machineGroups.put("xg-dal-c6-14", 4);

    machineGroups.put("xg-dal-c6-15", 5);
    machineGroups.put("xg-dal-c6-16", 5);
    machineGroups.put("xg-dal-c6-17", 5);
    machineGroups.put("xg-dal-c6-18", 5);

    machineGroups.put("xg-dal-c6-19", 6);
    machineGroups.put("xg-dal-c6-20", 6);
    machineGroups.put("xg-dal-c6-21", 6);
    machineGroups.put("xg-dal-c6-22", 6);

    machineGroups.put("xg-dal-c6-23", 8);
    machineGroups.put("xg-dal-c6-24", 8);
    machineGroups.put("xg-dal-c6-25", 8);
    machineGroups.put("xg-dal-c6-26", 8);

    machineGroups.put("xg-dal-c6-27", 9);
    machineGroups.put("xg-dal-c6-28", 9);
    machineGroups.put("xg-dal-c6-29", 9);
    machineGroups.put("xg-dal-c6-30", 9);

    machineGroups.put("xg-dal-c6-31", 10);
    machineGroups.put("xg-dal-c6-32", 10);
    machineGroups.put("xg-dal-c6-33", 10);
    machineGroups.put("xg-dal-c6-34", 10);

    machineGroups.put("xg-dal-c6-35", 11);
    machineGroups.put("xg-dal-c6-36", 11);
    machineGroups.put("xg-dal-c6-37", 11);
    machineGroups.put("xg-dal-c6-38", 11);
    machineGroups.put("xg-dal-c6-39", 11);
    machineGroups.put("xg-dal-c6-40", 11);
    machineGroups.put("xg-dal-c6-41", 11);
    machineGroups.put("xg-dal-c6-42", 11);

    machineGroups.put("xg-dal-c4-7", 12);
    machineGroups.put("xg-dal-c4-8", 12);

    machineGroups.put("xg-dal-c4-15", 13);
    machineGroups.put("xg-dal-c4-16", 13);
    machineGroups.put("xg-dal-c4-17", 13);
    machineGroups.put("xg-dal-c4-18", 13);
    machineGroups.put("xg-dal-c4-19", 13);
    machineGroups.put("xg-dal-c4-20", 13);
    machineGroups.put("xg-dal-c4-21", 13);
    machineGroups.put("xg-dal-c4-22", 13);

    return machineGroups;
}
```

## 设计思路

- 先将map里面的value都抽取出来存放在一个集合里面
- 利用groupingby对上面求出来的集合进行分组
- 之后要获取哪组的大小，直接get相应的machineGroupid然后取size()，就能得到结果了

## 代码实现

```java
public static void main(String[] args) {
    Map<String, Integer> map = initMachineGroup();
    Collection<Integer> values = map.values();
    System.out.println(values.stream().collect(Collectors.groupingBy(c -> c)));
    Map<Integer, List<Integer>> m = values.stream().collect(Collectors.groupingBy(c -> c));
    System.out.println("m.get(8).size() = "+m.get(8).size());
}
```

## 运行效果

获取value为8的元素的个数

> {2=[2, 2, 2], 3=[3, 3, 3, 3], 4=[4, 4, 4, 4], 5=[5, 5, 5, 5], 6=[6, 6, 6, 6], 8=[8, 8, 8, 8], 9=[9, 9, 9, 9], 10=[10, 10, 10, 10], 11=[11, 11, 11, 11, 11, 11, 11, 11], 12=[12, 12], 13=[13, 13, 13, 13, 13, 13, 13, 13]}
>
> m.get(8).size() =  4