---
layout: post
title: 283.Move Zeroes(Easy)
date: 2016-12-05 14:33
categories: LeetCode
tags: [LeetCode,Easy,数组]
---

>Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.

>For example, given `nums = [0, 1, 0, 3, 12]`, after calling your function, `nums` should be `[1, 3, 12, 0, 0]`.
给定一个num数组，把数组中的0都移动到最后去

**Note**:

>1.  You must do this **in-place** without making a copy of the array.
2.  Minimize the total number of operations.
不允许复制这个数组要在数组本身操作

# My Solution
### (Java) Version 1  Time: 20ms:
　　第一思路就是从前往后遍历，找到第一个为0的位置，就让它和后面第一个不为0的位置交换，这样就可以把后面所有的非0交换到前面来
```java
public class Solution {
    public void moveZeroes(int[] nums) {
        if(nums.length==0||nums.length==1)return;
        for(int i=0;i<nums.length-1;i++){
            if(nums[i]==0){
                int j=0;
                for(j=i+1;j<nums.length;j++){
                    if(nums[j]!=0){
                        nums[i]=nums[j];
                        nums[j]=0;
                        break;
                    }
                }
            }
        }
        return;
    }
}
```
### (Java) Version 2  Time: 0ms:
　　回头转念一想就觉得不对，既然要0放到最后，那何必搞两次遍历，直接把不是0的元素按照1234在前面排一列就是了，然后就有了这个0ms的方法
```java
public class Solution {
    public void moveZeroes(int[] nums) {
        if(nums.length==0||nums.length==1)return;
        int i=0,j=0,temp;
        for(i=0;i<nums.length;i++){
            if(nums[i]!=0){
                temp=nums[i];
                nums[i]=nums[j];
                nums[j]=temp;
                j++;
            }
        }
    }
}
```
　　这两个方法也是discuss里面的大多数的两种方案，更慢的就不看了