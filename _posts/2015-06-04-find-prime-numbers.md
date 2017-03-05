---
layout: post
title: 1262.寻找素数对
date: 2015-06-04 21:06
categories: 杭电HDU
tags: [杭电HDU,搜索]
---
## Problem
>**Problem Description**  
哥德巴赫猜想大家都知道一点吧.我们现在不是想证明这个结论,而是想在程序语言内部能够表示的数集中,任意取出一个偶数,来寻找两个素数,使得其和等于该偶数.  
做好了这件实事,就能说明这个猜想是成立的.  
由于可以有不同的素数对来表示同一个偶数,所以专门要求所寻找的素数对是两个值最相近的.  
**Input**  
输入中是一些偶整数M(5<M<=10000).  
**Output**  
对于每个偶数,输出两个彼此最接近的素数,其和等于该偶数.  
**Sample Input**  
20 30 40  
**Sample Output**  
7 13  
13 17  
17 23  

## Solution
```cpp
#include<stdio.h>
#include<math.h>
int sushu(int x)
{
    int i;
    for(i=2;i<=sqrt(x);i++){
        if(x%i==0)return 1;
    }
    return 0;
}
int main(void)
{
    int x,i;
    while(scanf("%d",&x)!=EOF){
        for(i=x/2;i>0;i--){
            if(sushu(i)==0&&sushu(x-i)==0)break;
        }
        printf("%d %d\n",i,x-i);
    }
    return 0;
} 
```