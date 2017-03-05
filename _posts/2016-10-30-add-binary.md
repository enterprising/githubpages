---
layout: post
title: 67.Add Binary(Easy)
date: 2016-10-30 23:14
categories: LeetCode
tags: [LeetCode,Easy,大数运算,数组]
---

>Given two binary strings, return their sum (also a binary string).
给两个二进制的字符串，返回它们的和

**Example**
>a = "11"
b = "1"
Return "100"

# My Solution
### (Java) Version 1  Time: 4ms:
　　这个其实没有太复杂的原理，基本上只要是字符串表示数都是大数运算的套路，先把字符串逆序，然后高位补0，然后用循环逐位相加，对进位的情况进行处理，然后就得到结果了，复杂的是进位的几种情况的处理
```java
public class Solution {
    public String addBinary(String a, String b) {
        StringBuffer s1 = new StringBuffer(a);
        StringBuffer s2 = new StringBuffer(b);
        int index_a = s1.length(),index_b = s2.length(),index = 0;
        s1.reverse();s2.reverse();
        if(index_a>index_b){
            index = index_a;
            for(int i = index_b;i<index_a;i++)s2.append("0");
        }else{
            index = index_b;
            for(int i = index_a;i<index_b;i++)s1.append("0");
        }
        char[] c1 = s1.toString().toCharArray();
        char[] c2 = s2.toString().toCharArray();
        StringBuffer sb = new StringBuffer();
        int flag = 0;
        for(int i = 0;i<index;i++){
            if(c1[i]=='0'&&c2[i]=='0'&&flag==0)sb.append("0");
            else if(c1[i]=='0'&&c2[i]=='0'&&flag==1){sb.append("1");flag=0;}
            else if(c1[i]=='1'&&c2[i]=='1'&&flag==0){sb.append("0");flag=1;}
            else if(c1[i]=='1'&&c2[i]=='1'&&flag==1){sb.append("1");flag=1;}
            else if(flag==1){sb.append("0");flag=1;}
            else sb.append("1");
            if(i == index-1&&flag==1)sb.append("1");
        }
        return sb.reverse().toString();
    }
}
```
### (Java) Version 2  Time: 4ms (By [lianglinMan](https://discuss.leetcode.com/user/lianglinman)):
　　这个思路号称是3ms但是实测是5ms，不过思路也是挺有意思的，先加，然后再做进位处理，比我的这种方式显然要简洁多了
```java
public class Solution {
    public String addBinary(String a, String b) {
        int len1 = a.length(), len2 = b.length(),end1 = len1,end2 = len2;
        int maxLen = Math.max(len1,len2);
        int size = 0;
        int[] binarys = new int[maxLen + 1];
        while(size <= maxLen){
            if(size < len1) {
                binarys[size] += a.charAt(--end1) - '0';
            }
            if(size < len2) {
                binarys[size] += b.charAt(--end2) - '0';
            }
            if(binarys[size] >= 2){
                binarys[size] %= 2;
                binarys[size + 1] = 1;
            }
            size++;
        }

       StringBuffer buffer = new StringBuffer();
       if(binarys[maxLen] == 1){
           buffer.append(1);
       }
       for(int i = maxLen - 1; i >=0; i--){
           buffer.append(binarys[i]);
       }
       return buffer.toString();
    }
}
```