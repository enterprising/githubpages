---
layout: post
title: 2002.计算球体积
date: 2014-09-12 21:36
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
根据输入的半径值，计算球的体积。  
**Input**  
输入数据有多组，每组占一行，每行包括一个实数，表示球的半径。  
**Output**  
输出对应的球的体积，对于每组输入数据，输出一行，计算结果保留三位小数。  
**Sample Input**  
1  
1.5  
**Sample Output**  
4.189  
14.137  
**Hint**  
```#define PI 3.1415927```  

## Solution
```cpp
#include<stdio.h>
#define PI 3.1415927
int main(void)
{
    double r,tiji;
    
    while(scanf("%lf",&r)!=EOF){
        tiji=(4*r*r*r*PI)/3;
        printf("%.3lf\n",tiji);
    }
    return 0;
}
```