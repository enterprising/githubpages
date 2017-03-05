---
layout: post
title: 2004.成绩转换
date: 2014-09-13 23:06
categories: 杭电HDU
tags: [杭电HDU]
---
## Problem
>**Problem Description**  
输入一个百分制的成绩t，将其转换成对应的等级，具体转换规则如下：  
90~100为A;  
80~89为B;  
70~79为C;  
60~69为D;  
0~59为E;  
**Input**  
输入数据有多组，每组占一行，由一个整数组成。  
**Output**  
对于每组输入数据，输出一行。如果输入数据不在0~100范围内，请输出一行：“Score is error!”。  
**Sample Input**  
56  
67  
100  
123  
**Sample Output**  
E  
D  
A  
Score is error!  

## Solution
```cpp
#include<stdio.h>
int main(void)
{
    int t;

	while(scanf("%d",&t)!=EOF){
		if(t<=100&&t>=90)
			printf("A\n");
		else if(t<=89&&t>=80)
			printf("B\n");
		else if(t<=79&&t>=70)
			printf("C\n");
		else if(t<=69&&t>=60)
			printf("D\n");
		else if(t<=59&&t>=0)
			printf("E\n");
		else
			printf("Score is error!\n");
	}
	return 0;
}
```