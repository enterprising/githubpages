---
layout: post
title: 415.Add Strings(Easy)
date: 2016-10-30 23:39
categories: LeetCode
tags: [LeetCode,Easy,大数运算,数组]
---

>Given two non-negative numbers num1
 and num2
 represented as string, return the sum of num1
 and num2
不解释了，就是大数相加

**Note:**
>1.The length of both *num1* and *num2* is < 5100.
2.Both *num1* and *num2* contains only digits 0-9.
3.Both *num1* and *num2* does not contain any leading zero.
4.You **must not use any built-in BigInteger library** or **convert the inputs to integer** directly.

# My Solution
### (Java) Version 1  Time: 26ms:
　　不解释了，就是大数相加，逆序补0然后处理进位
```java
public class Solution {
    public String addStrings(String num1, String num2) {
        StringBuffer s1 = new StringBuffer(num1);
        StringBuffer s2 = new StringBuffer(num2);
        s1.reverse();
        s2.reverse();
        int length = 0 , length_1 = s1.length() , length_2 = s2.length();
        if(length_1 > length_2){
            length = length_1;
            for(int i = length_2;i < length_1;i++)s2.append("0");
        }else{
            length = length_2;
            for(int i = length_1;i < length_2;i++)s1.append("0");
        }
        int flag = 0;
        StringBuffer result = new StringBuffer();
        char[] c1 = s1.toString().toCharArray();
        char[] c2 = s2.toString().toCharArray();
        for(int i = 0;i < length;i++){
            int a = c1[i] - 48;
            int b = c2[i] - 48;
            int c = a + b + flag;
            if(c >= 10){
                result.append(c-10);
                flag = 1;
            }else{
                result.append(c);
                flag = 0;
            }
            if(i == length - 1&&flag != 0)result.append(flag);
        }
        return result.reverse().toString();
    }
}
```
### (Java) Version 2  Time: 24ms (By [ratchapong.t](https://discuss.leetcode.com/user/ratchapong-t)):
　　简洁……我就难做到，对一些情况的总结还是不如高手们到位
```java
public class Solution {
    public String addStrings(String num1, String num2) {
        int i = num1.length() - 1;
        int j = num2.length() - 1;
        int carry = 0;
        char[] num1Array = num1.toCharArray();
        char[] num2Array = num2.toCharArray();
        StringBuilder sb = new StringBuilder();
        while (i >= 0 || j >= 0 || carry == 1) {
            int a = i >= 0 ? (num1Array[i--] - '0') : 0;
            int b = j >= 0 ? (num2Array[j--] - '0') : 0;
            int sum = a + b + carry;
            sb.insert(0, sum % 10);
            carry = sum / 10;
        }
        return sb.toString();
    }
}
```