---
layout: post
title: 6.ZigZag Conversion(Easy)
date: 2016-11-01 23:15
categories: LeetCode
tags: [LeetCode,Easy]
---

>The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)
![](http://olwt21mf4.bkt.clouddn.com/17-2-25/69818604-file_1487994663514_628b.png)

>And then read line by line: "PAHNAPLSIIGYIR"

>Write the code that will take a string and make this conversion given a number of rows:
```
string convert(string text, int nRows);
```
```
convert("PAYPALISHIRING", 3)
```
should return "PAHNAPLSIIGYIR".

*　　题意并不难了理解，就是对一个字符串进行一个重新排列按新的规则输出*

# My Solution
### (Java) Version 1  Time: 71ms:
　　确实是慢，我对这类找规律的题目的解法大体都是找到一个类似的规律，然后用if语句来补全
```java
public class Solution {
    public String convert(String s, int numRows) {
        int length=s.length();
        if(numRows==1||length<=numRows)return s;
        int itemCount=2*numRows-2;
        System.out.println("itemCount="+itemCount);
        int count=length%itemCount==0?length/itemCount:length/itemCount+1;
        System.out.println("count="+count);
        StringBuffer sb=new StringBuffer();
        for(int i=0;i<numRows;i++){
            for(int j=1;j<=count;j++){
                if(i==0)sb.append(s.charAt((j-1)*itemCount));
                else if(i==numRows-1)
                    if((j*itemCount-(numRows-2))-1<length){
                        sb.append(s.charAt((j*itemCount-(numRows-2))-1));
                    }
                    else{
                        continue;
                    }
                else {
                    if(((j-1)*itemCount+i)<length)sb.append(s.charAt((j-1)*itemCount+i));
                    if((itemCount*j-i)<length)sb.append(s.charAt(itemCount*j-i));
                }
            }
        }
        return sb.toString();
    }
}
```
### (Java) Version 2  Time: ms (By [medi](https://discuss.leetcode.com/user/medi)):
　　比我慢，纯粹是记一下不同的做法
```java
public class Solution {
    public String convert(String s, int numRows) {
        String res="";
        List<String> strow = new ArrayList<>();
        for(int i=0; i<numRows; i++){
            strow.add(0, "");
        } 
        int row=0;
        boolean turn=false;
        for(int i=0; i<s.length(); i++){
            char c = s.charAt(i);
            String news = strow.get(row)+String.valueOf(c);
            strow.remove(row);
            strow.add(row,news);
            if(!turn)
            row++;
            else{
            row--;
            }   
            row=row%numRows;
        
            if(row==0 && !turn){
            row=numRows-2;
            if(row<0)
                row=0;
            turn^= true;
            }
        
            if(row==0 && turn){
            row=0;
            turn^= true;
            }
        }
    
    for(String st: strow){
        res=res+st;
    }
    
    return res;
    }
}
```