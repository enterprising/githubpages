---
layout: post
title: 1094.A+B for Input-Output Practice (VI)
date: 2014-10-27 19:03
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
Your task is to calculate the sum of some integers.  
**Input**  
Input contains multiple test cases, and one case one line. Each case starts with an integer N, and then N integers follow in the same line.  
**Output**  
For each test case you should output the sum of N integers in one line, and with one line of output for each line in input.   
**Sample Input**  
4 1 2 3 4  
5 1 2 3 4 5  
**Sample Output**  
10  
15  

## Solution
```cpp
#include<stdio.h>
int main(void)
{
    int n,m,i,sum;
    
    while(scanf("%d",&n)!=EOF){
        sum=0;
        for(i=0;i<n;i++){
            scanf("%d",&m);
            sum+=m;
        }
        printf("%d\n",sum);
    }
    return 0;
}
```
