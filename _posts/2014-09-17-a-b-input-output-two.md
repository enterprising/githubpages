---
layout: post
title: 1090.A+B for Input-Output Practice (II)
date: 2014-09-17 17:12
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
Your task is to Calculate a + b.  
**Input**  
Input contains an integer N in the first line, and then N lines follow. Each line consists of a pair of integers a and b, separated by a space, one pair of integers per line.   
**Output**  
For each pair of input integers a and b you should output the sum of a and b in one line, and with one line of output for each line in input.   
**Sample Input**  
2  
1 5  
10 20  
**Sample Output**  
6  
30  

## Solution
```cpp
#include<stdio.h>
int main()
{
    int a[1000];
    int line,x,y,i,j;

    while(scanf("%d",&line)!=EOF){
        for(i=1;i<=line;i++){
            scanf("%d %d",&x,&y);
            a[i]=x+y;                                 
        }
        for(j=1;j<=line;j++){
            printf("%d\n",a[j]);
        }
    }
    return 0;
}
```