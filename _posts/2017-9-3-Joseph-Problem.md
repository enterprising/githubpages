---
layout: post
title: 【算法】约瑟夫环
date: 2017-9-3 14:00
categories: 算法
tags: [算法]
---

* content
{:toc} 
# 简介

约瑟夫环（[约瑟夫问题](https://baike.baidu.com/item/%E7%BA%A6%E7%91%9F%E5%A4%AB%E9%97%AE%E9%A2%98)）是一个数学的应用问题：已知n个人（以编号1，2，3...n分别表示）围坐在一张圆桌周围。从编号为k的人开始报数，数到m的那个人出列；他的下一个人又从1开始报数，数到m的那个人又出列；依[此](https://baike.baidu.com/item/%E6%AD%A4)规律重复下去，直到圆桌周围的人全部出列。通常解决这类问题时我们把编号从0~n-1，最后[1][ ]() 结果+1即为原问题的解。

# 概述

约瑟夫环运作如下：

1、一群人围在一起坐成 环状（如：N）

2、从某个编号开始报数（如：K）

3、数到某个数（如：M）的时候，此人出列，下一个人重新报数

4、一直循环，直到所有人出列 ，约瑟夫环结束

# 一定要能写出来的版本

思路描述：

1. 定义一个长度为n的数组；
2. 数组初始化的时候默认值为0，为0的时候我们就认为这个元素还在，为-1代表不在了；
3. 循环，用一个变量step记录每次走的步数，当step==m的时候，这是对于元素为-1；
4. 直到只剩下一个；

代码：

```java
public static int findLastNumber(int n,int m){
  if(n<1||m<1) return -1;
  int[] array = new int[n];
  int i = -1,step = 0, count = n;
  while(count>0){   //跳出循环时将最后一个元素也设置为了-1
    i++;          //指向上一个被删除对象的下一个元素。
    if(i>=n) i=0;  //模拟环。
    if(array[i] == -1) continue; //跳过被删除的对象。
    step++;                     //记录已走过的。
    if(step==m) {               //找到待删除的对象。
      array[i]=-1;
      step = 0;
      count--;
    }        
  }
  return i;//返回跳出循环时的i,即最后一个被设置为-1的元素
}
```

# 高级版：公式推导

这个属于递推问题了。

公式：x`=(x+k)%n;

f[i] 表示 i 个人玩游戏，报m退出，最后胜利者的编号。

我们要求的是f[n]

递推公式：

> f[1] = 0;
>
> f[i] = (f[i-1] + m) % i ;

代码：

```java
public static int getNumber(int n,int m){
  	if(n==0)
      return -1;
  	if(n==1)
      return 0;
  	else
      return (getNumber(n-1,m)+m)%n; 
}
```