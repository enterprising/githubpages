---
layout: post
title: 9.Palindrome Number(Easy)
date: 2016-11-16 14:31
categories: LeetCode
tags: [LeetCode,Easy,回文数]
---

>Determine whether an integer is a palindrome. Do this without extra space.
判断一个数是否为回文数，不要使用额外的空间

*　　回文数就是顺序和倒叙写出来是同样的数的数，比如121,12321等*

# My Solution
### (Java) Version 1  Time: 195ms:
　　既然是顺序倒序都一样的话，那就构造倒序的数，如果和前面的数相等的话，那就是回文数咯，从前往后，最高位的变成新数的最低位，以此类推
```java
public class Solution {
    public boolean isPalindrome(int x) {
        if(x<0)return false;
        else if(x<10)return true;
        int temp,num1=x,num2=0;
        while(num1>=1){
            temp=num1%10;
            num2=num2*10+temp;
            num1/=10;
        }
        if(num2==x)return true;
        return false;
    }
}
```
### (Java) Version 2  Time: 269ms (By [cbmbbz](https://discuss.leetcode.com/user/cbmbbz)):
　　和我的做法一致……不过看起来简洁，就是不知道为什么时间会长这么多
```java
public class Solution {
    public boolean isPalindrome(int x) {
        if (x<0 || (x!=0 && x%10==0)) return false;
        int rev = 0;
        while (x>rev){
            rev = rev*10 + x%10;
            x = x/10;
        }
        return (x==rev || x==rev/10);
    }
}
```
### (Java) Version 3  Time: 183ms (By [asser](https://discuss.leetcode.com/user/asser)):
　　似乎用了什么黑魔法……
　　作者的解释：
  This solution compares the least significant digit with the most significant digit just like you would do with a string, then get rid of those digits and repeat.
　　Example:
　　1.  x = 3428243
　　2.  d = 1000000
　　3.  (x%10) = 3, also (x/d) = 3, so keep going
　　4.  (x/d)\*d = 3000000
　　5.  x - 3000000 = 428243
　　6.  x/10 = 42824
　　7.  d/100 = 10000*

　　Repeat till x = 0 (true) or break if step 3 fails (false)
```java
public class Solution {
    public boolean isPalindrome(int x) {
        if(x<0) return false;
        int d = 1;
        while(x/d>=10) d*=10;
        while(x>0){
            if(x%10 != x/d) return false;
            x = (x-(x/d)*d)/10;
            d/=100;
        }
        return true;
    }
}
```