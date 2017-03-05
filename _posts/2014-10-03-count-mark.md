---
layout: post
title: 1235.统计同成绩学生人数
date: 2014-10-03 23:34
categories: 杭电HDU
tags: [杭电HDU,计数]
---
## Problem
>**Problem Description**  
读入N名学生的成绩，将获得某一给定分数的学生人数输出。  
**Input**  
测试输入包含若干测试用例，每个测试用例的格式为  
第1行：N  
第2行：N名学生的成绩，相邻两数字用一个空格间隔。  
第3行：给定分数  
当读到N=0时输入结束。其中N不超过1000，成绩分数为（包含）0到100之间的一个整数。  
**Output**  
对每个测试用例，将获得给定分数的学生人数输出。  
**Sample Input**  
3  
80 60 90  
60  
2  
85 66  
0  
5  
60 75 90 55 75  
75  
0   
**Sample Output**  
1  
0  
2  
**Hint**  
*Huge input, scanf is recommended.*  

## Solution
```cpp
#include<stdio.h>
int main(void)
{
    int count,num,sign,a[1000],i;
    
    while(scanf("%d",&count)!=EOF){
        if(count==0){
            break;
        }
        num=0;
        for(i=0;i<count;i++){
            scanf("%d",&a[i]);
        }
        scanf("%d",&sign);
        for(i=0;i<count;i++){
            if(a[i]==sign){
                num++;
            }
        }
        printf("%d\n",num);
    }
    return 0;
} 
```