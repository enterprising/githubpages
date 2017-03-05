---
layout: post
title: 500.Keyboard Row(Easy)
date: 2017-02-23 11:26
categories: LeetCode
tags: [LeetCode,Easy,搜索,一行实现]
---

>Given a List of words, return the words that can be typed using letters of alphabet on only one row's of American keyboard like the image below.

![Keyboard](http://olwt21mf4.bkt.clouddn.com/17-2-26/32288848-file_1488083862684_1335f.png)

*　　虽然还把键盘搬出来了，但是实际上只是搜索每一个String的字母是否在键盘的同一行出现，键盘的每一行只要用一个String代替就ok了，剩下的问题显然就简单了。*

**For example**

>Input: ["Hello", "Alaska", "Dad", "Peace"]
Output: ["Alaska", "Dad"]

# My Solution
### (Java) Version 1  Time: 3ms:
　　简单的实现方式，反正每次写了很多重复代码的话我就觉得这样的写法肯定是很low的，flag用到烂了，用来表示各种状态，然后再判断，没有什么技术含量……
```java
public class Solution {
    public String[] findWords(String[] words) {
        int flag;
        List<String> resultlist = new ArrayList<String>();
        String[] keyboard = { "qwertyuiop", "asdfghjkl", "zxcvbnm" };
        for (String s : words) {
            String ss = s.toLowerCase();
            flag = 0;
            for (char c : ss.toCharArray()) {
                if (keyboard[0].indexOf(c) != -1) {
                    if (flag == 0 || flag == 1) {
                        flag = 1;
                        continue;
                    } else {
                        flag = 4;
                        break;
                    }
                } else if (keyboard[1].indexOf(c) != -1) {
                    if (flag == 0 || flag == 2) {
                        flag = 2;
                        continue;
                    } else {
                        flag = 4;
                        break;
                    }
                } else if (keyboard[2].indexOf(c) != -1) {
                    if (flag == 0 || flag == 3) {
                        flag = 3;
                        continue;
                    } else {
                        flag = 4;
                        break;
                    }
                }
            }
            if (flag != 4)
                resultlist.add(s);
        }
        String[] result = (String[]) resultlist.toArray(new String[resultlist.size()]);
        return result;
    }
}
```
### (Java) Version 2  Time: 2ms (By [vineetpalan81](https://discuss.leetcode.com/user/vineetpalan81)):
　　这个解法和我的差不多，但是巧妙的地方在于大小写，它直接把小写的字母放进判断的字符串中，而不是把待测字符串全部转为小写，这样的做法从结果上来看，显然是快的，而且它没有立flag
```java
public class Solution 
{
    String[] rows = new String[]{"QWERTYUIOPqwertyuiop","ASDFGHJKLasdfghjkl","ZXCVBNMzxcvbnm"};
        
    public String[] findWords(String[] words) 
    {
        ArrayList<String> result = new ArrayList<String>();
    
        int currentRow = 0;
        boolean found = true;
        for(String s : words)
        {
            currentRow = getCurrentRow(s.charAt(0));
            found = true;
            for(char c: s.toCharArray())
            {
                if(rows[currentRow].indexOf(c) == -1)
                {
                    found = false;
                    break;
                }
            }
            if(found)
               result.add(s);
        }
        
        return result.toArray(new String[result.size()]);
    }
    
    private int getCurrentRow(char c)
    {
       for(int rowNumber = 0; rowNumber < 3 ; rowNumber++)
       {
           if (rows[rowNumber].indexOf(c) != -1)
           {
               return rowNumber;
           }
       }
       return -1;
    }
}
```
### (Java) Version 3  Time: 90ms (By [lixx2100](https://discuss.leetcode.com/user/lixx2100)):
　　一行解法虽然因为一般都很费时间而没有实际用处，但是这也是对技巧的高度提炼，这个一行解法用到了正则表达式，时间肯定长很多，但是解法很巧妙，只能说瑕不掩瑜
```java
public class Solution {
    public String[] findWords(String[] words) {
        return Stream.of(words).filter(s -> s.toLowerCase().matches("[qwertyuiop]*|[asdfghjkl]*|[zxcvbnm]*")).toArray(String[]::new);
    }
}
```