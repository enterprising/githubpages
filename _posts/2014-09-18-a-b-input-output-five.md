---
layout: post
title: 1093.A+B for Input-Output Practice (V)
date: 2014-09-18 10:55
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
Your task is to calculate the sum of some integers.  
**Input**  
Input contains an integer N in the first line, and then N lines follow. Each line starts with a integer M, and then M integers follow in the same line.   
**Output**  
For each group of input integers you should output their sum in one line, and with one line of output for each line in input.   
**Sample Input**  
2  
4 1 2 3 4  
5 1 2 3 4 5  
**Sample Output**  
10  
15  

## Solution
```cpp
#include<stdio.h>
int main()
{
    
    int count,i,num,j,c,zongshu;
    int a[1000];
    
    scanf("%d",&count);
    for(i=0;i<count;i++){
        scanf("%d",&num);
        zongshu=0;
        a[i]=0;
        for(j=0;j<num;j++){
            scanf("%d",&c);
            a[i]+=c;
        }
    }
    for(i=0;i<count;i++){
        printf("%d\n",a[i]);
    }
    return 0;
}
```
