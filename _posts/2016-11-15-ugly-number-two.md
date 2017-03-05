---
layout: post
title: 264.Ugly Number II(Medium)
date: 2016-11-15 14:30
categories: LeetCode
tags: [LeetCode,Medium,DFS]
---

>Write a program to find the `n`-th ugly number.

>Ugly numbers are positive numbers whose prime factors only include `2, 3, 5`. 
For example, `1, 2, 3, 4, 5, 6, 8, 9, 10, 12` is the sequence of the first `10` ugly numbers.

>Note that `1` is typically treated as an ugly number.

![](http://olwt21mf4.bkt.clouddn.com/17-2-26/64812465-file_1488082906974_13b13.png)

*　　题意就不翻译了，意思就是把丑数从小到大排列，输出制定位置的那个丑数是多少，按照上一题的那种朴素判断当然是行得通的，只要把1~n的数全部都验证一遍，自然可以找到第n个，但是认真想想，一旦n大了之后，其实丑数的个数是不多的，也就是说验证成功的次数远远小于失败的，所以用朴素的验证方式，显然是相当浪费时间（事实上也是），所以我们的思路就是想到如何一个一个计算出丑数，找到其中的规律，主动去计算丑数而不是被动得验证是不是丑数*

# My Solution
### (Java) Version 1  Time: 131ms:
　　计算丑数的原理当然不难，不要想到这一点还是花了不少功夫，首先每个丑数都是由2,3,5相乘得来的，而且每一个相同乘数的个数还不定，比如有5个5，3个1之类的，所以只能从小到大一个一个算出来，然后我们又发现了每个丑数其实都是由前面小的丑数乘以2,3,5得来的，那就很明显了，我们只要把前面的丑数都乘以2,3,5，然后结果里面最小的那个就一定是下一个丑数了
```java
public class Solution {
    public int nthUglyNumber(int n) {
        int[] nums = new int[n];
        nums[0]=1;
        int max2=0,max3=0,max5=0,index;
        for(int i=0;i<n-1;i++){
            index=0;
            while(max2<=nums[i]){
                max2=nums[index]*2;
                index++;
            }
            index=0;
            while(max3<=nums[i]){
                max3=nums[index]*3;
                index++;
            }
            index=0;
            while(max5<=nums[i]){
                max5=nums[index]*5;
                index++;
            }
            nums[i+1]=max2<max3?max2<max5?max2:max5<max3?max5:max3:max3<max5?max3:max5;
        }
        return nums[n-1];
    }
}
```
### (Java) Version 2  Time: 46ms (By [luke.wang.7509](https://discuss.leetcode.com/user/luke-wang-7509)):
　　这个解法的生成形式和上一个并无不同，不过上一个解法每一次都要从第一个丑数开始乘显然是没有必要的，因为有很多重复，这里就避免了这个问题
```java
public class Solution {
    public int nthUglyNumber(int n) {
        if(n<=0) return 0;
        int a=0,b=0,c=0;
        List<Integer> table = new ArrayList<Integer>();
        table.add(1);
        while(table.size()<n)
        {
            int next_val = Math.min(table.get(a)*2,Math.min(table.get(b)*3,table.get(c)*5));
            table.add(next_val);
            if(table.get(a)*2==next_val) a++;
            if(table.get(b)*3==next_val) b++;
            if(table.get(c)*5==next_val) c++;
        }
        return table.get(table.size()-1);
    }
}
```
### (Java) Version 3  Time: 4ms (By [TheKingRingReal](https://discuss.leetcode.com/user/thekingringreal)):
　　DFS是深度优先的搜索算法，这里我还没能理解，不过从结果上看，已经很显然了……4ms快到妈都不认得是谁
　　作者的解释：
![](http://olwt21mf4.bkt.clouddn.com/17-2-26/75490918-file_1488082964975_fd4d.png)
we can get a tree like this which contains all the ugly number ;just use three point to dfs because of I can not find the relationship between three point ; so we can think it just like this picture
![](http://olwt21mf4.bkt.clouddn.com/17-2-26/36738126-file_1488082973482_1604d.png)
each point 3 and 5 can have the 2_3and 2_5;3_5 child.and we can use a tricky in programming that if we meet 2_3=6 we should p2++;p3++;if 3*5:p5++;p3++;just like the code writ in DFS function
```java
public class Solution {
    public int nthUglyNumber(int n) {
        int[] dp=new int[n];dp[0]=1;
        return DFS(dp,1,0,0,0,n);
    }

    private int DFS(int[] dp, int i, int p2, int p3, int p5, int n) {
        if (i==n)return dp[n-1];
        dp[i]=Math.min(dp[p2]*2, Math.min(dp[p3]*3,dp[p5]*5));
        if (dp[i]==dp[p2]*2)p2++;
        if(dp[i]==dp[p3]*3)p3++;
        if (dp[i]==dp[p5]*5)p5++;
        return DFS(dp, i+1, p2, p3, p5, n);
    }
}
```
### (Java) Version 4  Time: 2ms (By [zmajia](https://discuss.leetcode.com/user/zmajia)):
　　这个哈哈哈哈哈哈逗逼把测试样例的极限试了出来，然后打表了哈哈哈哈哈哈，实在是太坏了，所以获得了最快的速度，已知极限用打表的方式空间换时间还是很管用的
```java
public class Solution {
    static int[] save = new int[3000];
    static int flag = 0;
    public int nthUglyNumber(int n) {
        if(flag == 0){
            flag ++;
            init();
        }
        return save[n-1];
    }
    public static void init(){
        int _2 = 0, _3 = 0, _5 = 0;
        save[0] = 1;
        for(int i = 1; i < 3000; i++){
            int min = Math.min(save[_2]*2, Math.min(save[_3]*3,save[_5]*5));
            if(save[_2]*2 == min) {
                save[i] = save[_2]*2;
                _2 ++;
            }
            if(save[_3]*3 == min) {
                save[i] = save[_3]*3;
                _3 ++;
            }
            if(save[_5]*5 == min) {
                save[i] = save[_5]*5;
                _5 ++;
            }
        }
    }
}
```
### (Java) Version 5  Time: 108ms (By [saharH](https://discuss.leetcode.com/user/saharh)):
　　TreeSet我没有怎么研究过，虽然慢但这是我看到的最简洁的做法，其中起作用的应该就是TreeSet的一些特性了，值得研究
```java
public class Solution {
    public int nthUglyNumber(int n) {
        TreeSet<Long> hs = new TreeSet<>(Arrays.asList(1l, 2l, 3l, 5l));
        Long e = 1l;
        while( n != 0){
            e = hs.pollFirst();
            hs.add(e * 2);
            hs.add(e * 3);
            hs.add(e * 5);
            n--;
        }
        return e.intValue();
    }
}
```