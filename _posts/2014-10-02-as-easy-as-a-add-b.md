---
layout: post
title: 1040.As Easy As A+B
date: 2014-10-02 19:42
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
These days, I am thinking about a question, how can I get a problem as easy as A+B? It is fairly difficulty to do such a thing. Of course, I got it after many waking nights.  
Give you some integers, your task is to sort these number ascending (升序).  
You should know how easy the problem is now!  
Good luck!  
**Input**  
Input contains multiple test cases. The first line of the input is a single integer T which is the number of test cases. T test cases follow. Each test case contains an integer N (1<=N<=1000 the number of integers to be sorted) and then N integers follow in the same line.   
It is guarantied that all integers are in the range of 32-int.  
**Output**  
For each case, print the sorting result, and one line one case.  
**Sample Input**  
2  
3 2 1 3  
9 1 4 7 2 5 8 3 6 9  
**Sample Output**  
1 2 3  
1 2 3 4 5 6 7 8 9  

## Solution
```cpp
#include<stdio.h>
int main(void)
{
    int count1,count2,i,j,k,l,o,t,a[10000];
    
    scanf("%d",&count1);
    for(i=0;i<count1;i++){
        scanf("%d",&count2);
        for(j=0;j<count2;j++){
            scanf("%d",&a[j]);
        }
        for(k=0;k<count2-1;k++){
            for(l=0;l<count2-1-k;l++){
                if(a[l]>a[l+1]){
                    t=a[l];
                    a[l]=a[l+1];
                    a[l+1]=t;
                }
            }
        }
        for(o=0;o<count2;o++){
            if(o==0){
                printf("%d",a[o]);
            }
            else{
                printf(" %d",a[o]);
            }
        }
        printf("\n");
    }
    return 0;
}
```