---
layout: post
title: 26.Remove Duplicates from Sorted Array(Easy)
date: 2016-10-10 17:34
categories: LeetCode
tags: [LeetCode,Easy,数组]
---

>Given a sorted array, remove the duplicates in place such that each element appear only *once* and return the new length.
Do not allocate extra space for another array, you must do this in place with constant memory.
给定一个已排序的数组，去掉其中重复的元素，使得每个元素只出现一次，并返回新数组的长度。
不要为数组分配额外的空间，你必须在常量内存中进行操作

*　　意思不难理解，就是一个已经排好序的数组然后把不同的数抽出来组成一个新的数组，这里说的不要额外分配空间的意思是不要new一个新的数组，直接在原来传进来的数组上进行操作，从第一位开始重新构建一个长度为返回值得数组即可，长度后面有没有元素都没有关系，不用管*

**For example**
>Given input array *nums* = [1,1,2],Your function should return length = 2, with the first two elements of *nums* being 1 and 2 respectively. It doesn't matter what you leave beyond the new length.

# My Solution
### (Java) Version 1  Time: 15ms:
　　因为数组已经排好序了，所以只要从前面向后遍历，遇到不一样的数就放到前面去，自此组成新的数组
```java
public class Solution {
    public int removeDuplicates(int[] nums) {
        int p=0,length=nums.length;
        for(int i=0;i<length;i++){
            if(nums[i]!=nums[p]){
                p++;
                nums[p]=nums[i];
            }
        }
        return p+1;
    }
}
```
### (Java) Version 2  Time: 13ms (By [MadDetective](https://discuss.leetcode.com/user/maddetective)):
　　这道题应该是没有什么优化的空间了，都是O(n)复杂度，没有什么好对比，习惯性贴一个略有不同的实现，时间自然是没有优劣之分
```java
public class Solution {
    public int removeDuplicates(int[] nums) {
        if(nums==null) return 0;
        int i=0, j=1;
        while(j<nums.length){
            if(nums[i]==nums[j]){
                j++;
            }else {
                i++;
                nums[i] = nums[j]; 
                j++;
            }
        }
        return i+1;
    }
}
```