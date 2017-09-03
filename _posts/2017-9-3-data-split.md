---
layout: post
title: 【算法】数据分割
date: 2017-9-3 9:00
categories: 算法
tags: [算法]
---

* content
{:toc} 
原文链接：<http://blog.csdn.net/xiao_dondon/article/details/77804072>

题目：

![](http://img.blog.csdn.net/20170902214736323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGlhb19kb25kb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

BFS（广度优先搜索），遍历所有可能的分割情况，求出最接近的数字。

代码：

```java
import java.util.*;  
  
public class Main {  
  
    public static void main(String[] args) {  
        Scanner in = new Scanner(System.in);  
        while (in.hasNext()) {  
            int n = in.nextInt();  
            int m = in.nextInt();  
            List<Integer> list = new ArrayList<>();  
            String s = String.valueOf(n);  
            for(int i=0;i<s.length();i++){  
                list.add(s.charAt(i)-'0');  
            }  
            List<Integer> l = new ArrayList<>();  
            fon(0,list,l,m);  
            int min = -1;  
            for(int i : l){  
                if(i>min)  
                    min = i;  
            }  
            System.out.println(min);  
        }  
    }  
    public static void fon(int sum,List<Integer> list,List<Integer> l,int m){  
        if(list.size() == 0)  
            l.add(sum);  
        else{  
            int temp = list.get(0);  
            while(sum+temp <= m){  
                list.remove(0);  
                fon(sum+temp,new ArrayList<Integer>(list),l,m);  
                if(list.size() == 0)  
                    break;  
                else{  
                    temp = temp*10 + list.get(0);  
                }  
            }  
        }  
    }  
}  
```

