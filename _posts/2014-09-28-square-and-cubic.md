---
layout: post
title: 2007.平方和与立方和
date: 2014-09-28 17:27
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
给定一段连续的整数，求出他们中所有偶数的平方和以及所有奇数的立方和。  
**Input**  
输入数据包含多组测试实例，每组测试实例包含一行，由两个整数m和n组成。  
**Output**  
对于每组输入数据，输出一行，应包括两个整数x和y，分别表示该段连续的整数中所有偶数的平方和以及所有奇数的立方和。  
你可以认为32位整数足以保存结果。  
**Sample Input**  
1 3  
2 5  
**Sample Output**  
4 28  
20 152  

## Solution
```cpp
#include<stdio.h>
int main(void)
{
    int t,m,n,i;
    int sum1,sum2;
    
    while(scanf("%d %d",&m,&n)!=EOF){
        
        sum1=sum2=0;
        if(m>n){
            t=m;
            m=n;
            n=t;
        }
        
        
        for(i=m;i<=n;i++){
            
            if(i%2==0){
                sum1=sum1+i*i;
            }
            if(i%2!=0){
                sum2=sum2+i*i*i;
            }
        }
        printf("%d %d\n",sum1,sum2);
    }
    
    return 0;
} 
```