---
layout: post
title: 217.Contains Duplicate(Easy)
date: 2016-11-13 14:14
categories: LeetCode
tags: [LeetCode,Easy,数组,计数,一行实现]
---

>Given an array of integers, find if the array contains any duplicates.
 Your function should return true if any value appears at least twice in the array, 
and it should return false if every element is distinct.
给定一个整数数组，如果数组里面有任意一个值至少出现两次及以上，则函数返回true，如果每个元素都不同则返回false

# My Solution
### (Java) Version 1  Time: 14ms:
　　第一次用到了set，好开心，然而并没有什么用，用list来代替也是可以的，不过应该是接近最大时间了，在leetcode上提交经常是一次AC一次超时的
```java
public class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashSet s = new HashSet();
        for(int i=0;i<nums.length;i++){
            if(s.contains(nums[i]))return true;
            else s.add(nums[i]);
        }
        return false;
    }
}
```
### (Java) Version 2  Time: 3ms (By [dyvsay](https://discuss.leetcode.com/user/dyvsay)):
　　这个套路我还不是很懂……值得研究
```java
public class Solution {
    public boolean containsDuplicate(int[] nums) {
        byte[] mark = new byte[150000];
        for (int i : nums) {
            int j = i/8;
            int k = i%8;
            int check = 1<<k;
            if ((mark[j] & check) != 0) {
                return true;
            }
            mark[j]|=check;
        }
        return false;
    }
}
```
### (Java) Version 3  Time: 4ms (By [whspecial](https://discuss.leetcode.com/user/whspecial)):
　　这个套路在于先排序，为什么排序会这么快，看来以前觉得先排序会浪费时间这个想法是不对的，不知道Java里面默认的排序是哪种
```java
public class Solution {
    public boolean containsDuplicate(int[] nums) {
        if (nums.length <= 1) {
            return false;
        }
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] == nums[i+1]) {
                return true;
            }
        }
        return false;
    }
}
```
### (Java) Version 4  Time: 86ms (By [wit](https://discuss.leetcode.com/user/wit)):
　　一行KO问题的骚套路总是有很多人追求的！虽然真的慢……
```java
/*
*这是一行的套路
*/
public class Solution {
    public boolean containsDuplicate(int[] nums) {
         return Arrays.stream(nums).boxed().collect(Collectors.toSet()).size() < nums.length;
    }
}
```

```java
/*
*这是两行的套路
*/
public class Solution {
    public boolean containsDuplicate(int[] nums) {
         Set<Integer> set = new HashSet<>();
         return Arrays.stream(nums).filter(i -> !set.add(i)).findAny().isPresent();
    }
}
```