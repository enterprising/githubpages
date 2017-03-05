---
layout: post
title: 263.Ugly Number(Easy)
date: 2016-11-14 14:29
categories: LeetCode
tags: [LeetCode,Easy]
---

>Write a program to check whether a given number is an ugly number.
Ugly numbers are positive numbers whose prime factors only include `2, 3, 5`. 
For example, `6, 8` are ugly while `14` is not ugly since it includes another prime factor `7`.
Note that `1` is typically treated as an ugly number.

*　　丑数就是分解因子之后只含有2,3,5的数就是丑数，题目是判断一个整数是不是丑数，简单到只有这一种解法，没有别的了*

# My Solution
### (Java) Version 1  Time: 2ms:
*　　判断性质先跟着定义走，既然丑数是又2,3,5相乘得来的，那就除回去，先是用2除，除到除不尽了，就用3除，再用5除，如果是丑数的话，那肯定最后会得到1，那这就是丑数*
```java
public class Solution {
    public boolean isUgly(int num) {
        if(num==1||num==2||num==3||num==5)return true;
        if(num==0)return false;
        while(num%2==0)num/=2;
        while(num%3==0)num/=3;
        while(num%5==0)num/=5;
        if(num==1)return true;
        return false;
    }
}
```