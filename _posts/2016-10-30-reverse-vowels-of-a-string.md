---
layout: post
title: 345.Reverse Vowels of a String(Easy)
date: 2016-10-30 22:55
categories: LeetCode
tags: [LeetCode,Easy,字符串处理,堆栈]
---

>Write a function that takes a string as input and reverse only the vowels of a string.
**Note:**The vowels does not include the letter "y".
写一个函数把字符串中的元音字母的顺序调转，‘Y’不是一个元音字母

**Example 1:**
>Given s = "hello", return "holle".

**Example 2:**
>Given s = "leetcode", return "leotcede".

# My Solution
### (Java) Version 1  Time: 5ms:
　　先一次遍历把元音字母的下标记下来，然后把通过下标直接把字符串中的元音字母逆序，只需要创建一个简单的int数组
```java
public class Solution {
    public String reverseVowels(String s) {
        if(s==null)return null;
        if(s.length()<2)return s;
        char[] ch = s.toCharArray();
        int[] index = new int[ch.length];
        int length = 0;
        for(int i = 0;i < ch.length;i++){
            char c = ch[i];
            if(c=='a'||c=='e'||c=='i'||c=='o'||c=='u'||c=='A'||c=='E'||c=='I'||c=='O'||c=='U'){
                index[length] = i;
                length++;
            }
        }
        for(int i = 0;i < length/2;i++){
            char temp = ch[index[i]];
            ch[index[i]] = ch[index[length-1-i]];
            ch[index[length-1-i]] = temp;
        }
        return String.valueOf(ch);
    }
}
```
### (Java) Version 2  Time: 4ms (By [ZUN005](https://discuss.leetcode.com/user/zun005)):
　　另一种方法，直接从前和从后同时向中间遍历，两边遇到了元音字母就交换，这样循环的次数少，速度快了不少
```java
public class Solution {
    public String reverseVowels(String s) {
        char[] chars = s.toCharArray();
        char temp;
        int i = 0, j = chars.length - 1;

        while(i < j) {
            while(i < j && !vowel(chars[i])) 
                i++;
            while(i < j && !vowel(chars[j])) 
                j--;
            if(i < j) {
                temp = chars[i];
                chars[i] = chars[j];
                chars[j] = temp;
                i++;
                j--;
            }
        }
        return new String(chars);
}
    public boolean vowel(char c) {
        switch(c){
            case 'a':
            case 'e':
            case 'i':
            case 'o':
            case 'u':
            case 'A':
            case 'E':
            case 'I':
            case 'O':
            case 'U':
            return true;
            default:
            return false;
         }
    }
}
```
### (Java) Version 3  Time: 22ms (By [iamash](https://discuss.leetcode.com/user/iamash)):
　　慢虽然慢，但是思路很有意思，这里用了堆栈的性质，先进后出，这本身就是一个逆序的操作，简单遍历一遍把原因字母压入堆栈中，然后又依次取出来，就自然形成了逆序，但是两个完整的循环和堆栈操作显然让时间长了不少
```java
public class Solution {
    public String reverseVowels(String s) {
        StringBuilder res = new StringBuilder(s);
        Stack<Character> stack= new Stack<>();
        for (char c: s.toCharArray()){
            if (c =='a' || c =='e'||c =='i'||c =='o'||c =='u'
            ||c =='A' || c =='E'||c =='I'||c =='O'||c =='U'){
                stack.push(c);
            }
        }
        for (int i=0; i<s.length(); i++){
            if (s.charAt(i) =='a' || s.charAt(i) =='e'||s.charAt(i) =='i'||s.charAt(i) =='o'||s.charAt(i) =='u'
            ||s.charAt(i) =='A' || s.charAt(i) =='E'||s.charAt(i) =='I'||s.charAt(i) =='O'||s.charAt(i) =='U'){
                res.setCharAt(i, stack.pop());
            }
        }
        return res.toString();
    }
}
```