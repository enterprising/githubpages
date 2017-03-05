---
layout: post
title: 344.Reverse String(Easy)
date: 2016-10-05 17:12
categories: LeetCode
tags: [LeetCode,Easy,位运算,字符串处理]
---

>Write a function that takes a string as input and returns the string reversed.
编写一个函数，输入一个字符串，并输出其倒序的字符串。

**Example:**
>Given s = "hello", 
return "olleh".

# My Solution
### (Java) Version 1  Time: 8ms:
　　倒序字符串，简单地把字符串从结尾开始取出来，放到一个新的StringBuffer中，然后输出
```java
public class Solution {
    public String reverseString(String s) {
        StringBuffer sb = new StringBuffer();
        for (int i = s.length() - 1; i >= 0; i--) {
            sb.append(s.charAt(i));
        }
        return sb.toString();
    }
}
```
### (Java) Version 2  Time: 3ms:
　　倒序字符串事实上并不需要把整个字符串倒序输出，而是只要将其对半，然后对其中的一半进行逐字调换即可，这样遍历整个字符串变成了只遍历一半的字符串
```java
public class Solution {
    public String reverseString(String s) {
        char[] chars = s.toCharArray();
        char c;
        for (int i = s.length() - 1, j = 0; j != chars.length / 2; i--, j++) {
            c = chars[j];
            chars[j] = chars[i];
            chars[i] = c;
        }
        return String.valueOf(chars);
    }
}
```
### (Java) Version 3  Time: 4ms:
　　有一个代码量很少的方式，但是似乎用Java自带的工具方法并不是最快的
```java
public class Solution {
    public String reverseString(String s) {
        return new StringBuffer(s).reverse().toString();
    }
}
```
### (Java) Version 4  Time: 2ms(By [Shans.Xia](https://discuss.leetcode.com/user/shans-xia)):
　　多了两个判断，应该会直接略过很多例子，然后再循环中使用i<=j作为条件，让arrChar.length只需要计算一次，提高了些许的速度
```java
public class Solution {
        public String reverseString(String s) {
            if (s == null)
                return null;
            if (s.equals(""))
                return s;
            char[] arrChar = s.toCharArray();
            for (int i = 0, j = arrChar.length - 1; i <= j; i++, j--) {
                char temp = arrChar[i];
                arrChar[i] = arrChar[j];
                arrChar[j] = temp;
            }
            return new String(arrChar);
        }
    }
```
### (Java) Version 5  Time: 2ms(By [anderson1993](https://discuss.leetcode.com/user/anderson1993)):
　　如果追求速度的话，应该不止我一个人会觉得应该多用位运算吧，一个新颖的思路，这个解答也是两秒，应该是在交换的时候位运算对速度的提升有限，或者测试样例不够极端吧
```java
public class Solution {
    public String reverseString(String s) {
        char[] str = s.toCharArray();
            int begin = 0;
            int end = s.length() - 1;
            while (begin < end) {
                str[begin] = (char) (str[begin] ^ str[end]);
                str[end] = (char) (str[begin] ^ str[end]);
                str[begin] = (char) (str[end] ^ str[begin]);
                begin++;
                end--;
            }
            return new String(str);
    }
}
```