---
layout: post
title: 233.Number of Digit One（暂留）
date: 2016-10-07 17:36
categories: LeetCode
tags: [LeetCode,Medium,搜索,计数]
---

>Given an integer n, count the total number of digit 1 appearing in all non-negative integers less than or equal to n.
给出一个整数n，计算在所有小于或等于n的非负整数中，数字1出现的个数。

*　　这里最麻烦的自然就是当数字大的时候，用简单的循环暴力计数肯定会超时，这个时候考验的就是找规律的能力了*

**For example:**
>Given n = 13,
Return 6, because digit 1 occurred in the following numbers: 1, 10, 11, 12, 13.

# My Solution
### (Java) Version 1  Time: *Time Limit Exceeded*:
　　这里先记录一个单纯的可爱的直观的暴力解法，当然这个肯定是会超时的，记录一下以作对比。对每一个数转化为string的字符数组然后一个个数，没错，赤果果地超时了，超时的输入是824883294，eclipse也花了我切换几次网页的时间才跑出结果
```java
public class Solution {
    public int countDigitOne(int n) {
        int count = 0;
        for (int i = 0; i <= n; i++) {
            String s = String.valueOf(i);
            for (char c : s.toCharArray()) {
                if (c == '1') {
                    count++;
                }
            }
        }
        return count;
    }
}
```