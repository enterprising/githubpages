---
layout: post
title: 1279.验证角谷猜想
date: 2014-10-05 14:18
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
数论中有许多猜想尚未解决，其中有一个被称为“角谷猜想”的问题，该问题在五、六十年代的美国多个著名高校中曾风行一时，这个问题是这样描述的：任何一个大于一的自然数，如果是奇数，则乘以三再加一；如果是偶数，则除以二；得出的结果继续按照前面的规则进行运算，最后必定得到一。现在请你编写一个程序验证他的正确性。  
**Input**  
本题有多个测试数据组，第一行为测试数据组数N，接着是N行的正整数。  
**Output**  
输出验证“角谷猜想”过程中的奇数，最后得到的1不用输出；每个测试题输出一行；每行中只有两个输出之间才能有一个空格；如果没有这样的输出，则输出：No number can be output !。  
**Sample Input**  
4  
5  
9  
16  
11  
**Sample Output**  
5  
9 7 11 17 13 5  
No number can be output !  
11 17 13 5  

## Solution
```cpp
#include<stdio.h>
int main(void)
{
    int count,sign,i,a,t;
    
    while(scanf("%d",&count)!=EOF){
        for(i=0;i<count;i++){
            sign=1;
            scanf("%d",&a);
            t=a;
            while(1){
                if(t%2==0){
                    t=t/2;
                }
                if(t==1){
                    printf("\n");
                    break;
                }
                if(t%2!=0&&t!=1){
                    if(sign==1){
                        printf("%d",t);
                        t=t*3+1;
                        sign=2;
                    }
                    else{
                        printf(" %d",t);
                        t=t*3+1;
                    }
                }
                if(sign==1){
                    printf("No number can be output !");
                    sign=3;
                }
            }
        }
    }
    return 0;
}
```