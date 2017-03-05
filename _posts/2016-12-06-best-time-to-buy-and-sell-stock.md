---
layout: post
title: 121. Best Time to Buy and Sell Stock(Easy)
date: 2016-12-06 14:33
categories: LeetCode
tags: [LeetCode,Easy,动态规划,一行实现]
---

>Say you have an array for which the _i_ element is the price of a given stock on day _i_.
给定一个数组，其中第i个元素是第i天是这个股票的价格

>If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), 
design an algorithm to find the maximum profit.
如果你仅被允许交易一次（即买一支股票和卖一支股票），设计一个算法来找到最大的利润

*　　这个题目有点绕，其实就是在一个数组中找到差值（利润）最大的两个数，但是关键在于题目设定的卖出的价格必须大于买进的价格，也就是后面的数要大于前面的数，这就难了，如果是通常的判断，势必要每一个数都要判断一遍，因为还没有循环到后面的事后你不知道到底后面有没有更大的数*

**For example 1**
>Input: [7, 1, 5, 3, 6, 4]
Output: 5

>max. difference = 6-1 = 5 (not 7-1 = 6, as selling price needs to be larger than buying price)

**For example 2**
>Input: [7, 6, 4, 3, 1]
Output: 0

>In this case, no transaction is done, i.e. max profit = 0.

# My Solution
### (Java) Version 1  Time: Time Limit Exceeded:
　　朴素的超时的方法
```java
public class Solution {
    public int maxProfit(int[] prices) {
        int min=0;
        for(int i=0;i<prices.length-1;i++){
            for(int j=i+1;j<prices.length;j++){
                int temp=prices[i]-prices[j];
                if(temp<min)min=temp;
            }
        }
        return -min;
    }
}
```
### (Java) Version 2  Time: 2ms:
　　解决超时的问题，首先肯定是减小计算量，我们可以发现在超时算法中有很多不必要的判断步骤，比如[1,2,3,4]中，如果我们找到了1和4那么判断2,3显然是多余，所以可以以找到的第一个最大的数为界（这个数的下一个比它小），然后计算多个“最大值”然后比较这些最大值找到最大的值
```java
public class Solution {
    public int maxProfit(int[] prices) {
        if(prices.length==1||prices.length==0)return 0;
        int max=0,tempmax=0,a=prices[0],b=prices[0];
        for(int i=1;i<prices.length;i++){
            if(prices[i]>b){
                b=prices[i];
                tempmax=b-a;
            }else if(prices[i]<a){
                max=tempmax>max?tempmax:max;
                a=prices[i];
                b=prices[i];
                tempmax=b-a;
            }else{
                continue;
            }
        }
        return tempmax>max?tempmax:max;
    }
}
```
### (Java) Version 3  Time: 2ms (By [reddy2005](https://discuss.leetcode.com/user/reddy2005)):
　　从头开始遍历，遇到小于min的就存下了，遇到大于min的就把这个数减去min的差保存下来，然后让max在后面跟着判断，找到最大的
```java
public class Solution {
    public int maxProfit(int[] prices) {
        int len=prices.length;
        if(len==0) return 0;
    
        int min=prices[0],max=0;
        for(int i=0;i<len;i++){
            if(prices[i]<min) min=prices[i];
            prices[i]-=min;
            if(max<prices[i]) max=prices[i];
        }
        return max;
    }
}
```
### (Java) Version 4  Time: 3ms (By [tiffanyTown](https://discuss.leetcode.com/user/tiffanytown)):
　　据说这是一个DP解法，然而DP我并不是很懂，所以只能留到以后再看了
```java
public class Solution {
    public int maxProfit(int[] prices) {
        if(prices.length<2) return 0;
        int[] dp=new int[prices.length];
        int minPrice=prices[0];
        dp[0]=0;
        for(int i=1;i<prices.length;i++){
            if(prices[i]>=minPrice){
                dp[i]=Math.max(dp[i-1],prices[i]-minPrice);
            }
            else{
                minPrice=prices[i];
                dp[i]=dp[i-1];
            }
        }
        return dp[prices.length-1];
    }
}
```
### (Java) Version 5  Time: 3ms (By [anton15](https://discuss.leetcode.com/user/anton15)):
　　总有些人就是想搞个大新闻，疯狂使用Java自带函数，来达到尽可能少的代码，这很6
```java
/*
*Proper Java - 6 lines:
*/
public class Solution {
    public int maxProfit(int[] prices) {
        int min = Integer.MAX_VALUE, max = 0;
        for (int i = 0; i < prices.length; i++) {
            min = Math.min(min, prices[i]);
            max = Math.max(max, prices[i] - min);
        }
        return max;
    }
}
```

```java
/*
*Proper Java with shortcuts - 4 lines:
*/
public class Solution {
    public int maxProfit(int[] prices) {
        int min = Integer.MAX_VALUE, max = 0;
        for (int i = 0; i < prices.length; i++)
            max = Math.max(max, prices[i] - (min = Math.min(min, prices[i])));
        return max;
    }
}
```

```java
/*
*Java 8 streams - 2 lines:
*/
public class Solution {
    int min = Integer.MAX_VALUE;
    public int maxProfit(int[] prices) {
        return Arrays.stream(prices).map(i -> i - (min = Math.min(min, i))).max().orElse(0);
    }
}
```