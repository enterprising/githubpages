---
layout: post
title: 1092.A+B for Input-Output Practice (IV)
date: 2014-09-17 17:27
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
Your task is to Calculate the sum of some integers.  
**Input**  
Input contains multiple test cases. Each test case contains a integer N, and then N integers follow in the same line. A test case starting with 0 terminates the input and this test case is not to be processed.  
**Output**  
For each group of input integers you should output their sum in one line, and with one line of output for each line in input.    
**Sample Input**  
4 1 2 3 4  
5 1 2 3 4 5  
0  
**Sample Output**  
10  
15  

## Solution
```cpp
#include<stdio.h> 
int main() 
{ 
    int n,i,m,sum; 
    while((scanf("%d",&n)!=EOF)&&(n!=0)) 
    { 
        sum=0; 
        i=1; 
        while(i<=n) 
        { 
            scanf("%d",&m); 
            { 
                sum=sum+m; 
            } 
            i++; 
        } 
        printf("%d\n",sum); 
    }
return 0; 
} 
```