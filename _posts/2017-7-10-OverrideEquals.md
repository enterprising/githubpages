---
layout: post
title: 重写equals方法
date: 2017-7-10 19:00
categories: Java
tags: [Java]
---

## 这个是非常重要的一个技术点

对象类：

```java
public class Group {
    private String groupName;
    private int load;

    get、set那些就不写了
      
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Group)) return false;
        Group group = (Group) o;
        return Objects.equals(getGroupName(), group.getGroupName());
    }

    @Override
    public int hashCode() {
        return Objects.hash(getGroupName(), getLoad());
    }
}
```

说明：上面的类，重写了equals方法，在这里面最重要的是最后一句，在最后一句只比较了groupName。所以就实现了：**只要name相同，就认为它们是同一个对象。**

测试代码：

```java
public static void main(String[] args) {
        List<Group> list = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            Group g = new Group();
            g.setGroupName(i + "");
            g.setLoad(i + 1000);
            list.add(g);
        }
        Group g1 = new Group();
        g1.setGroupName(0 + "");
        System.out.println(g1);
        System.out.println(list.get(0));
        System.out.println(g1.getGroupName() + " : " + g1.getLoad());
        System.out.println(list.get(0).getGroupName() + " : " + list.get(0).getLoad());
        System.out.println(g1.equals(list.get(0)));
    }
```

输出结果：

> me.ele.sample.test.group.Group@991  
> me.ele.sample.test.group.Group@d79  
> 0 : 0  
> 0 : 1000  
> true    
>
> Process finished with exit code 0  

根据结果发现： 

1、那两个对象所处的内存还是不一样的，所以用 == 比较的话会返回FALSE。

2、用equals方法比较，返回结果为TRUE

合理运用equals，能使逻辑清晰很多，代码短很多，程序健壮性好很多