---
layout: post
title: 342.Power of Four(Easy)
date: 2016-11-02 23:40
categories: LeetCode
tags: [LeetCode,Easy,位运算,一行实现,正则表达式]
---

>Given an integer (signed 32 bits), write a function to check whether it is a power of 4.
给定一个32位的整数，判断是否为4次幂

*　　一开始看到这题我就想到打表，但是，果然还是位运算最厉害*

**Example:**
>Given num = 16, return true. Given num = 5, return false.

**Follow up**
>Could you solve it without loops/recursion?

# My Solution
### (Java) Version 1  Time: 2ms:
　　没有什么好说的，在32位数的世界里面，4次幂并没有几个，只有15个数，直接存数组里面对比就是了，只是没有想到还是慢了
```java
public class Solution {
    public boolean isPowerOfFour(int num) {
        int[] integer = new int[]{1,4,16,64,256,1024,4096,16384,65536,262144,1048576,4194304,16777216,67108864,268435456,1073741824};
        for(int i=0;i<16;i++){
            if(num==integer[i])return true;
        }
        return false;
    }
}
```
### (Java) Version 2  Time: 3ms (By [k'](https://discuss.leetcode.com/user/k)):
　　不仅简洁还快，果然所有和2次幂扯上关系的，都可以让位运算来一脚
>　　老外的解释：
　　The mask is 01010101010101010101010101010101 (0x55555555), reason for that is any number that is power of 4 happens bit and other bit.

>　　For example for 8 bits:
　　4 ^ 0 = 00000001 (first bit is set)
　　4 ^ 1 = 00000100 ( third bit is set)
　　4 ^ 2 = 00010000 (5th bit is set)
　　and so on*

>　　So the mask would be 0x55555555 (this will take care of the sign bit too)*
　　The other part is that you want to make sure that only one bit is set in the number (similar to numbers of power 2), this is done by num == (num & -num)
　　num == (num & -num) will return true only if there was one bit set, the reason has to do about 2's complement representation.

>　　For example:
　　4 = 00000100
　　-4 = 11111011 + 1 = 11111100
　　so 00000100 & 11111100 = 00000100

>　　The only exception for this rule is if num is 0 which explains why we check num != 0
　　This solution would work for any number that is power of 2 (4, 8, 16, ..), you just need to adjust the mask

```java
public class Solution {
    public boolean isPowerOfFour(int num) {
        return num != 0 && (num & 0x55555555) == num && num == (num & -num);
    }
}
```
### (Java) Version 3  Time: 21ms (By [yfcheng](https://discuss.leetcode.com/user/yfcheng)):
　　有意思的从字符串出发的思路，虽然慢就慢了点
　　老外的解释：
>　　The idea is that numbers in quaternary system that is power of 4 will be like 10, 100, 1000 and such. Similar to binary case. And this can be extended to any radix.

```java
public class Solution {
    public boolean isPowerOfFour(int num) {
        return Integer.toString(num, 4).matches("10*");
    }
}
```
### (Java) Version 4  Time: ms (By [LeeLom](https://discuss.leetcode.com/user/leelom)):
　　似乎是一个通用的解法？
```java
public class Solution { 
    public boolean isPowerOfFour(int num) { 
        int x = (int)Math.sqrt(num); 
        //1073741824 is 4^15, 4^16 is bigger than int  
        return(num > 0 && 1073741824 % num == 0 && x*x == num); 
    }
}
```