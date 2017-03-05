---
layout: post
title: 2008.数值统计
date: 2014-09-22 09:54
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
统计给定的n个数中，负数、零和正数的个数。  
**Input**  
输入数据有多组，每组占一行，每行的第一个数是整数n（n\<100），表示需要统计的数值的个数，然后是n个实数；如果n=0，则表示输入结束，该行不做处理。  
**Output**  
对于每组输入数据，输出一行a,b和c，分别表示给定的数据中负数、零和正数的个数。  
**Sample Input**  
6 0 1 2 3 -1 0  
5 1 2 3 4 0.5  
0   
**Sample Output**  
1 2 3  
0 0 5  

## Solution
```cpp
#include<stdio.h>
int main(void)
{
    int count,i;
    int zhengshu,fushu,zero;
    double mark;
    
    while(scanf("%d",&count)!=EOF){
        
        fushu=zhengshu=zero=0;
        
        if(count==0){
            
            break;
        }
        else{

        for(i=1;i<=count;i++){
            
            scanf("%lf",&mark);
            
            if(mark<0){
                fushu++;
            }
            else if(mark==0){
                zero++;
            }
            else{
                zhengshu++;
            }
        }
        
        printf("%d %d %d\n",fushu,zero,zhengshu);
        
    }
    }
    
    return 0;
}
```