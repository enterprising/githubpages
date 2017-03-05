---
layout: post
title: 80.Remove Duplicates from Sorted Array II(Medium)
date: 2017-02-24 11:11
categories: LeetCode
tags: [LeetCode,Medium,计数,数组]
---

>Follow up for "Remove Duplicates":
What if duplicates are allowed at most twice?
给定一个有序的整数数列，每个数最多只能出现两次，然后去除多余的数，把剩下的数排在原数组的前面，并返回新数列的长度

*　　这虽然是个中等题，不过难度不大，唯一需要多考虑的就是在原数组前面排列新的数组*

**For example**
>Given sorted array nums = [1,1,1,2,2,3],
Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3. It doesn't matter what you leave beyond the new length.

# My Solution
### (Java) Version 1  Time: 1ms:
　　做出的方法还是各种立flag,，没什么多解释的，就是遍历一遍，然后跳过重复多的，然后符合标准的就放到前面去，甚至都不用交换
```java
public class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0)
            return 0;
        int count = 1, tempcount = 1, temp = nums[0], index = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] == temp) {
                if (tempcount < 2) {
                    tempcount++;
                    nums[index] = nums[i];
                    index++;
                    count++;
                }
            } else {
                temp = nums[i];
                tempcount = 1;
                nums[index] = nums[i];
                index++;
                count++;
            }
        }
        return count;
    }
}
```
### (Java) Version 2  Time: 0ms (By [StefanPochmann](https://discuss.leetcode.com/user/stefanpochmann)):
　　别人家的姿势水平就是高很多，能如此简单地解答，关键在于题目中给出的是有序数列，所以后面的数一定是比前面的大，或者说不同，就不用考虑后面还会有和前面相同数的情况
```java
public int removeDuplicates(int[] nums) {
    int i = 0;
    for (int n : nums)
        if (i < 2 || n > nums[i-2])
            nums[i++] = n;
    return i;
}
```