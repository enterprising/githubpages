---
layout: post
title: 374.Guess Number Higher or Lower(Easy)
date: 2017-02-23 10:18
categories: LeetCode
tags: [LeetCode,Easy,二分查找]
---

>We are playing the Guess Game. The game is as follows:
I pick a number from 1 to n. You have to guess which number I picked.
Every time you guess wrong, I'll tell you whether the number is higher or lower.
You call a pre-defined API guess(int num) which returns 3 possible results (-1, 1, or 0):
我们来玩一个猜数游戏，游戏规则是这样的：我从1~n中选择一个数，你需要猜我选的数是哪一个，如果你猜错的话，我就会告诉你你猜的数比我选择的数大还是小，你可以调用一个API guess(int num) ，返回三个可能的结果，-1表示比我的数小，1表示比我的数大，0表示猜中了。

*　　这个问题用二分查找即可，重点只在于确定查找的范围*

**For example**

>n = 10, I pick 6.
Return 6.

# My Solution
### (Java) Version 1  Time: 1ms:
　　用两个变量来表示上界和下界，每次都猜测范围1/2位置的数，然后根据返回的大小关系确定新的边界，踩过的坑是，如果直接用(up - down) / 2 + down来确定中间的数，那么将永远不会查找到作为边界的两个数，第二坑是我本来想用0~n+1来把边界的两个数放进范围里，但是果然测试里面有int的最大值，这就意味着n+1之后会溢出，所以只好在一开始单独对边界进行判断。
```java
/* The guess API is defined in the parent class GuessGame.
   @param num, your guess
   @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
      int guess(int num); */

public class Solution extends GuessGame {
    public int guessNumber(int n) {
        if(n == 1)return 1;
        else if(guess(n) == 0)return n;
        int down = 0, up = n;
        int key = (up - down) / 2;
        int flag = guess(key);
        while(flag != 0){
            if(flag == 1){
                down = key;
                key = (up - down) / 2 + down;
                flag = guess(key);
            }else{
                up = key;
                key = (up - down) / 2 + down;
                flag = guess(key);
            }
        }
        return key;
    }
}
```
### (Java) Version 2  Time: 1ms (By [LeetCode](https://leetcode.com/articles/guess-number-higher-or-lower/)):
　　来自官方的解答，二分查找，写法比我的高明得多，起码边界问题在循环中解决了，我第一时间没有想到的是解答中的循环条件……
　　时间复杂度为O(log2n)
```java
/* The guess API is defined in the parent class GuessGame.
   @param num, your guess
   @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
      int guess(int num); */

public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int low = 1;
        int high = n;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int res = guess(mid);
            if (res == 0)
                return mid;
            else if (res < 0)
                high = mid - 1;
            else
                low = mid + 1;
        }
        return -1;
    }
}
```
### (Java) Version 3  Time: 1ms (By [LeetCode](https://leetcode.com/articles/guess-number-higher-or-lower/)):
>　　来自官方的解答，三分查找
　　In Binary Search, we choose the middle element as the pivot in splitting. In Ternary Search, we choose two pivots (say ***m1*** and ***m2***) such that the given range is divided into three equal parts. If the required number numnum is less than ***m1*** then we apply ternary search on the left segment of ***m1***. If numnum lies between ***m1*** and ***m2***, we apply ternary search between ***m1*** and ***m2***. Otherwise we will search in the segment right to ***m2***.
　　简单来说就是我们在二分查找的时候把数平均分为两部分，在三分查找中我们把数平均分为三部分
　　时间复杂度为O(log3n)

```java
/* The guess API is defined in the parent class GuessGame.
   @param num, your guess
   @return -1 if my number is lower, 1 if my number is higher, otherwise return 0
      int guess(int num); */

public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int low = 1;
        int high = n;
        while (low <= high) {
            int mid1 = low + (high - low) / 3;
            int mid2 = high - (high - low) / 3;
            int res1 = guess(mid1);
            int res2 = guess(mid2);
            if (res1 == 0)
                return mid1;
            if (res2 == 0)
                return mid2;
            else if (res1 < 0)
                high = mid1 - 1;
            else if (res2 > 0)
                low = mid2 + 1;
            else {
                low = mid1 + 1;
                high = mid2 - 1;
            }
        }
        return -1;
    }
}
```