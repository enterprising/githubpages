---
layout: post
title: 同一个模块的三种不同写法
date: 2017-7-11 10:00
categories: Java
tags: [Java]
---

程序目的：

筛出来 groupQueue里面可以放在targetMachine上的最多的 group集合。

两个限制条件：1、group不能在targetMachine已经存在，2、group的负载加上targetMachine的原负载不能超过阈值。

三种写法：

## 1、思维混乱，但没错版

```java
/**
 * 该方法用于筛出来 groupQueue里面可以放在targetMachine上的最多的 group集合
 *
 * @param groupQueue    待移队列
 * @param targetMachine 目标机器
 * @return 返回一个group集合
 */
private LinkedList<Group> searchQueues(LinkedList<Group> groupQueue, Machine targetMachine) {
    LinkedList<Group> groupQueue_temp = new LinkedList<>(groupQueue);
    int tempCount = targetMachine.getHost().getCount();
    LinkedList<Group> subGroupQueues = new LinkedList<>();

    //先筛不在group里面的那些
    Group group = groupQueue_temp.peek();
    while (!groupQueue_temp.isEmpty() && tempCount + group.getCount() < max) {
        group = groupQueue_temp.poll();
        if (isGroupInThisHost(group, targetMachine)) {
            System.out.println(group.getGroupName() + " 已经存在了");
            continue;
        }
        if (group.getCount() + tempCount <= max) {
            subGroupQueues.offer(group);
            tempCount += group.getCount();
        }
    }
    System.out.println(subGroupQueues.size());
    return subGroupQueues;
}
```

点评：

​	1、用了很多不必要的内存空间，循环做的也很乱

​	2、判断语句的条件逻辑不是很合理

## 2、思维清晰，高级版

```java
private LinkedList<Group> searchQueues(LinkedList<Group> groupQueue, Machine targetMachine) {
    LinkedList<Group> subGroupQueues = new LinkedList<>();
    for (Group group : groupQueue) {
        System.out.println("233");
        if (isGroupInThisHost(group, targetMachine)) {
            System.out.println(group.getGroupName() + " 已经存在了");
            continue;
        }
        if (group.getCount() + targetMachine.getHost().getCount() <= max) {
            subGroupQueues.offer(group);
            targetMachine.getHost().setCount(targetMachine.getHost().getCount() + group.getCount());
        }
        if (group.getCount() + targetMachine.getHost().getCount() > max){
            break;
        }
    }
    System.out.println(subGroupQueues.size());
    return subGroupQueues;
}
```

点评：没有使用多余的内存空间，循环思路也比较清晰



## 3、进一步优化版

```java
private LinkedList<Group> searchQueues(LinkedList<Group> groupQueue, Machine targetMachine) {
    LinkedList<Group> subGroupQueues = new LinkedList<>();
    for (Group group : groupQueue) {
        if (isGroupInThisHost(group, targetMachine)) {
            continue;
        }
        if (group.getCount() + targetMachine.getHost().getCount() > max) {
            break;
        }
        subGroupQueues.offer(group);
        targetMachine.getHost().setCount(targetMachine.getHost().getCount() + group.getCount());
    }
    System.out.println(subGroupQueues.size());
    return subGroupQueues;
}
```

点评：循环的条件逻辑变得非常清晰