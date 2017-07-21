---
layout: post
title: 重写对象的equals方法和toString方法
date: 2017-7-10 19:00
categories: Java
tags: [Java]
---

* content
{:toc}
这个是非常重要的一个技术点

# 重写equals方法

有时候判断两个东西是不是同一个对象，我们只需要判断它们的某个属性是不是一样。但是系统默认的是比较内存地址，这就非常尴尬了。这时候我们就需要重写对象的equals方法。

## 对象类

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

## 测试代码

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

## 输出结果

> me.ele.sample.test.group.Group@991  
> me.ele.sample.test.group.Group@d79  
> 0 : 0  
> 0 : 1000  
> true    
>
> Process finished with exit code 0  

## 结论

根据结果发现： 

1、那两个对象所处的内存还是不一样的，所以用 == 比较的话会返回FALSE。

2、用equals方法比较，返回结果为TRUE

合理运用equals，能使逻辑清晰很多，代码短很多，程序健壮性好很多

3、虽然方便，但是一般不用。因**为equals和hashcode方法运行结果不一致，是大忌。**

4、重写equals方法的话，要满足**自反性、对称性、传递性、一致性、非空性**。

> 1   自反性：对任意引用值X，x.equals(x)的返回值一定为true.  
> 2   对称性：对于任何引用值x,y,当且仅当y.equals(x)返回值为true时，x.equals(y)的返回值一定为true;  
> 3   传递性：如果x.equals(y)=true, y.equals(z)=true,则x.equals(z)=true  
> 4   一致性：如果参与比较的对象没任何改变，则对象比较的结果也不应该有任何改变  
> 5   非空性：任何非空的引用值X，x.equals(null)的返回值一定为false

# 重写toString方法

很多时候我们输出list或者map的时候，出来一堆乱七八糟的hashcode，也就是它的地址码之类的东西。给我们带来了较大的麻烦。但是重写toString方法之后，就方便很多了。

## 对象类

```java
public class Host {
    private String hostName;
    private int load;
    private int groupid;

    @Override
    public String toString() {
        return getHostName();
    }
}
```

## 测试代码

```java
List<Host> list = new ArrayList();
list.add(new Host("A",1));
list.add(new Host("B",2));
System.out.println(list);
```

效果比对：

1、不重写toString方法时，输出：

> [me.ele.sample.test.host.Host@4f3f5b24, me.ele.sample.test.host.Host@15aeb7ab]

2、重写toString方法之后，输出：

> [A, B]