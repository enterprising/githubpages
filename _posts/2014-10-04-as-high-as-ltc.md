---
layout: post
title: 1407.测试你是否和LTC水平一样高
date: 2014-10-04 01:43
categories: 杭电HDU
tags: [杭电HDU,解方程]
---
## Problem
>**Problem Description**  
大家提到LTC都佩服的不行，不过，如果竞赛只有这一个题目，我敢保证你和他绝对在一个水平线上  
你的任务是：  
计算方程x^2+y^2+z^2= num的一个正整数解。  
**Input**  
输入数据包含多个测试实例，每个实例占一行，仅仅包含一个小于等于10000的正整数num。  
**Output**  
对于每组测试数据，请按照x,y,z递增的顺序输出它的一个最小正整数解，每个实例的输出占一行，题目保证所有测试数据都有解。  
**Sample Input**  
3  
**Sample Output**  
1 1 1  

## Solution
```cpp
#include<stdio.h>
#include<math.h>
int main(void)
{
    int x,i,j,k,sign,num,t;
    
    while(scanf("%d",&x)!=EOF){
        sign=0;
        for(i=1;i*i<x;i++){
            for(j=1;j*j<x;j++){
                for(k=1;k*k<x;k++){
                    t=i*i+j*j+k*k;
                    if(t==x){
                        sign=1;
                        break;
                    }
                }
                if(sign==1){
                    break;
                }
            }
            if(sign==1){
                break;
            }
        }
        printf("%d %d %d\n",i,j,k);
    }
    return 0;
}
```