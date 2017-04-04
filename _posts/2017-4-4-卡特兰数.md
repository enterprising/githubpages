---
layout: post
title: 卡特兰数
date: 2017-04-4 20:40
categories: 笔试面试
tags: [笔试面试]
---
## 卡特兰数公式
 总的排列C(2n,n)  
 非法组合的可能有C(2n,n+1)  
 结果是卡特兰数公式，即C(2n,n)/(n+1)
## 常见例题
遇到下面这些，直接用卡特兰数的公式就可以了，非常简单！  
![enter description here][1]  

![enter description here][2]  
![enter description here][3]  
```java
import java.util.*;
 
public class Parenthesis {
    public int countLegalWays(int n) {
        if(n<=0)
            return 0;
        // 总的排列C(2n,n)
        // 非法组合的可能有C(2n,n+1)
        // 结果是卡特兰数公式，即C(2n,n)/(n+1)
        int res=1;
        for(int i=2*n;i>=n+1;i--){
            res=res*i;
        }
        int di = 1;
        for(int j=1;j<=n;j++)
            di*=j;
        return res/di/(n+1);
    }
}
```

  [1]: http://omphwvjh0.bkt.clouddn.com/1491308015726.jpg
  [2]: http://omphwvjh0.bkt.clouddn.com/1491308183550.jpg
  [3]: http://omphwvjh0.bkt.clouddn.com/1491308204010.jpg