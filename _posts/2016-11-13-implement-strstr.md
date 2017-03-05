---
layout: post
title: 28.Implement strStr()(Easy)
date: 2016-11-13 12:24
categories: LeetCode
tags: [LeetCode,Easy,搜索,字符串处理,一行实现]
---

>Implement strStr().
Returns the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.
返回needle在haystack中第一次出现的位置的索引值，如果没有找到，就返回-1

*　　就是寻常的字符串匹配搜索，应该因为是简单题，所以朴素的循环比较也能过，事实上应该要用KMP算法的*

# My Solution
### (Java) Version 1  Time: 7ms:
　　这就是一个典型的朴素的两重循环比较的算法，没有什么好说的
```java
public class Solution {
    public int strStr(String haystack, String needle) {
        if(haystack.length()==needle.length()){
            if(haystack.length()==0)return 0;
            else if(haystack.equals(needle))return 0;
            else return -1;
        }
        else if(needle.length()==0)return 0;
        char[] c1=haystack.toCharArray();
        char[] c2=needle.toCharArray();
        for(int i=0;i<c1.length-c2.length+1;i++){
            for(int j=0;j<c2.length;j++){
                //System.out.println(c2[j]+"__"+c1[j+i]);
                if(c2[j]!=c1[j+i])
                    break;
                else if(j==c2.length-1)
                    return i;
            }
        }
        return -1;
    }
}
```
### (Java) Version 2  Time: 6ms (By [Kexin_Li](https://discuss.leetcode.com/user/kexin_li)):
　　讲道理，我内心的想法就是——这TMD也可以？？不是很懂contains的使用范围
```java
public class Solution {
    public int strStr(String haystack, String needle) {
        return haystack.contains(needle) ? haystack.indexOf(needle) : -1;
    }
}
```
### (Java) Version 3  Time: 6ms (By [Domenickd3](https://discuss.leetcode.com/user/domenickd3)):
　　这是用substring切割了字符串，然后做比较，事实上和两个循环并无多大不同，只是第二个循环用了Java自己的方法，也许对比直接用for循环会有优化
```java
public class Solution {
    public int strStr(String haystack, String needle) {
        for (int i = 0; i < haystack.length() - needle.length() + 1; ++i) {
            if (haystack.substring(i, i + needle.length()).equals(needle)) {
                return i;
            }
        }
        return -1;
    }
}
```