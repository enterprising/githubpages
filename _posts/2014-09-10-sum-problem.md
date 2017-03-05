---
layout: post
title: 1001.Sum Problem
date: 2014-09-10 16:45
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
Hey, welcome to HDOJ(Hangzhou Dianzi University Online Judge).  
In this problem, your task is to calculate SUM(n) = 1 + 2 + 3 + ... + n.  
计算1 + 2 + 3 + …… + n的值  
**Input**  
The input will consist of a series of integers n, one integer per line.  
**Output**  
For each case, output SUM(n) in one line, followed by a blank line. You may assume the result will be in the range of 32-bit signed integer.  
**Sample Input**  
1  
100  
**Sample Output**  
1  
5050  

## Solution
　　这个就是按照题意，直接for循环到n就可以解决了
```cpp
#include<stdio.h>
int main()
{
 int i,n,sum=0;
 while(scanf("%d",&n) != EOF) 
  { for(i=0;i<=n;i++)
     {sum=sum+i;}
      printf("%d\n\n",sum);
      sum=0;
  }
 return 0;
}
```