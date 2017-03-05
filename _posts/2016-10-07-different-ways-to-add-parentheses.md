---
layout: post
title: 241.Different Ways to Add Parentheses(Medium)
date: 2016-10-07 22:53
categories: LeetCode
tags: [LeetCode,Medium,递归,计数]
---

>Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are+ ,- and\*.
给定一个数字和运算符的字符串，返回所有不同分组情况下运算的结果。合法的运算符有+，-和\*

*　　题目的意思就是给一段只有+，-，\*的三则运算，然后加括号，加括号的时候符号不变，把加了括号后计算的不同结果列出来*

**Example 1**
>Input:"2-1-1".
((2-1)-1) = 0
(2-(1-1)) = 2
Output:[0, 2]

**Example 2**
>Input:"2*3-4*5"
(2*(3-(4*5))) = -34
((2*3)-(4*5)) = -14
((2*(3-4))*5) = -10
(2*((3-4)*5)) = -10
(((2*3)-4)*5) = 10
Output:[-34, -14, -10, -10, 10]

# My Solution
### (Java) Version 1  Time: 7ms:
　　思考括号的分组情况确实耗费了很长时间，一开始考虑是从数字开始分组，如果数字有奇数(n)个，那么就可以从第一个到第n/2个，分别加上括号，然后递归这个情况，知道只有两个运算数和一个运算符为止，然而，这个分类的方法实现起来相当麻烦，循环的操作十分复杂。然后从这个解法是从运算符开始分组，按照每一个运算符都是分为前一组和后一组，进行递归，就简单多了
```java
public class Solution {
    public List<Integer> diffWaysToCompute(String input) {
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < input.length(); i++) {
            char c = input.charAt(i); //搜索运算符
            if (c == '+' || c == '-' || c == '*') { //判断运算符
                String s_bef = input.substring(0, i); //取出前半部分
                String s_aft = input.substring(i + 1, input.length()); //取出后半部分
                List<Integer> l_bef = diffWaysToCompute(s_bef); //递归
                List<Integer> l_aft = diffWaysToCompute(s_aft);
                for (Integer a : l_bef) { //双循环，找到前后递归出来的所有数的计算排列
                    for (Integer b : l_aft) {
                        switch (c) {
                        case '+':
                            list.add(a + b);
                            break;
                        case '-':
                            list.add(a - b);
                            break;
                        case '*':
                            list.add(a * b);
                            break;
                        }
                    }
                }
            }
        }
        if (list.size() == 0) { //list.size()为零的时候就是跳出递归的时候，这个时候的input就只有一个数
            list.add(Integer.parseInt(input));
        }
        return list;
    }
}
```