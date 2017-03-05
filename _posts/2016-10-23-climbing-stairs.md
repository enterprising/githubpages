---
layout: post
title: 70.Climbing Stairs(Easy)
date: 2016-10-23 12:50
categories: LeetCode
tags: [LeetCode,Easy,递归]
---

>You are climbing a stair case. It takes *n* steps to reach to the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
你在爬楼梯，楼梯总共有n个台阶，每次你可爬一级或者两级台阶，问爬完整个楼梯有多少种爬的方式？

*　　这是以前小学的奥数题，找规律，爬楼梯，显然我们都知道对于一次只走一级或者两级的情况是符合斐波那契数列的，所以题目的答案就变成了求第n个斐波那契数是多少*

# My Solution
### (Java) Version 1  Time: 0ms:
　　应该是因为是Easy题，所以测试的样例相当少，求斐波那契数列有很多种方式，可以用迭代也可以用递归，递归的时间应该会增长很快，一般在确定的情况下用迭代会好，比如for循环什么的
```java
public class Solution {
    public int climbStairs(int n) {
        if(n==0)return 0;
        else if(n==1)return 1;
        else if(n==2)return 2;
        else{
            int a=1,b=2,c=0;
            while(n-2>0){
                c=a+b;
                a=b;
                b=c;
                n--;
            }
            return b;
        }
    }
}
```