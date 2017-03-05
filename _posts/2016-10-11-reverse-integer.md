---
layout: post
title: 7.Reverse Integer(Easy)
date: 2016-10-11 23:02
categories: LeetCode
tags: [LeetCode,Easy,溢出]
---

>Reverse digits of an integer.
反转一个整数的数字
**Example1:** x = 123, return 321
**Example2:** x = -123, return -321

*　　看似没有坑，实则不然，因为在这个题目里面，隐含的条件还有要做溢出处理，即输入一个超过Integer最大值的整数*

# My Solution
### (Java) Version 1  Time: 43ms:
　　首先想到的做法就是把整数当做个字符串进行反转，刚好前几天做过了反转字符串的操作，这里直接应用，然后就通过了，关键是用long类型来装传入的int值，就可以有效避免在运算中溢出的问题
```java
public class Solution {
    public int reverse(int x) {
        if(x>-10&&x<10)
            return x;
        long y = x;
        long z = Math.abs(y);
        boolean isNegative = x > 0;
        String s = String.valueOf(z);
        char[] chars = s.toCharArray();
        char c;
        for (int i = s.length() - 1, j = 0; j != chars.length / 2; i--, j++) {
            c = chars[j];
            chars[j] = chars[i];
            chars[i] = c;
        }
        z = Long.parseLong(String.valueOf(chars));
        if(z>Integer.MAX_VALUE)
            return 0;
        return (int)(isNegative ? z : -1 * z);
    }
}
```
### (Java) Version 2  Time: 41ms:
　　除去字符串操作，这个解法就是典型的直接对数字进行处理，从各位开始取数，然后一位一位取到最高位，组成一个新的数，就是结果，代码显然是简短了很多，处理仍然是用long作操作数，本来我以为上一个解法会比这个直接操作整数的解法更快，不过似乎被打脸了，应该是数转字符串耗费了不少的时间，也可能是因为题目的范围仅仅是在整数的范围里，长度较短，字符串反转的优势没有显现。当然，根据leetcode的尿性，这两个解法都是属于同一个时间复杂度的，并没有明显的差距
```java
public class Solution {
    public int reverse(int x) {
        long y;
        long z = x;
        boolean isNegative = x > 0;
        z = Math.abs(z);
        y = z % 10;
        z /= 10;
        while (z != 0) {
            y = y * 10 + z % 10;
            if (y > Integer.MAX_VALUE)
                return 0;
            z /= 10;
        }
        return (int)(isNegative ? y : -1 * y);
    }
}
```

　　发现discuss里其他的Java解法似乎也是大同小异，并没有更快的，都是40ms左右，但是同样是直接操作整数C++却只要6ms，这…………