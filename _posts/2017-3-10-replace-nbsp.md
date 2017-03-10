---
layout: post
title: 剑指offer-替换空格
date: 2017-1-1 8:20
categories: 剑指Offer
tags: [剑指Offer]
---
# 替换空格
> 题目描述：<br>
> 请实现一个函数，将一个字符串中的空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

# 思路1 空间复杂度较高的一种方法
> 从前往后遍历替换，用到StringBuffer（线程安全）。

## 代码：
```java
public class Solution {
     public String replaceSpace(StringBuffer str) {
      StringBuffer sb = new StringBuffer();
          for (int i = 0; i < str.length(); i++) {
               if(str.charAt(i)==' '){
                    sb.append("%20");
               }else{
                    sb.append(str.charAt(i));
               }
          }
          return sb.toString();
     }
 }

```

# 思路2 足够拿到Offer的一种方法
> 从后往前。 先遍历一遍整个字符串，统计出空格的总数，之后再重新给字符串长度赋值。从后往前遍历，遇到空格依次插入要加的字符串。

## 代码：
```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
        int spacenum = 0;//spacenum为计算空格数
        for(int i=0;i<str.length();i++){
            if(str.charAt(i)==' ')
                spacenum++;
        }
        int indexold = str.length()-1; //indexold为为替换前的str下标
        int newlength = str.length() + spacenum*2;//计算空格转换成%20之后的str长度
        int indexnew = newlength-1;//indexold为为把空格替换为%20后的str下标
        str.setLength(newlength);//使str的长度扩大到转换成%20之后的长度,防止下标越界
        for(;indexold>=0 && indexold<newlength;--indexold){ 
                if(str.charAt(indexold) == ' '){  //
                str.setCharAt(indexnew--, '0');
                str.setCharAt(indexnew--, '2');
                str.setCharAt(indexnew--, '%');
                }else{
                    str.setCharAt(indexnew--, str.charAt(indexold));
                }
        }
        return str.toString();
    }
}

```