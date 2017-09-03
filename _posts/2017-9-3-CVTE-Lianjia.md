---
layout: post
title: CVTE & 链家
date: 2016-12-1 9:00
categories: 笔试面试
tags: [笔试面试]
---

* content
{:toc} 
## 基于TCP和UDP的协议

TCP：**HTTP**,**FTP**,**SMTP**,TENET,POP3,Finger,NNTP,[IMAP4](https://www.baidu.com/s?wd=IMAP4&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1YkrjD4rjnsuHnLmy7-PjD30ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EPHnLPjn1njnL),
UDP：BOOTP,**DHCP**,NTP,**TFTP**,SNMP
DNS可以基于TCP，也可以基于UDP。

## 局域网的拓扑结构

星型、总线型、环形状

## 属于交换排序的算法有

- 交换类排序 - **冒泡排序** 鸡尾酒排序 奇偶排序 梳子排序 侏儒排序 **快速排序** 臭皮匠排序 Bogo排序
- 选择类排序 - **选择排序** **堆排序** Smooth排序 笛卡尔树排序 锦标赛排序 圈排序
- 插入类排序 - **插入排序** **希尔排序** 二叉查找树排序 图书馆排序 耐心排序
- 归并类排序 - **归并排序** 梯级归并排序 振荡归并排序 多相归并排序 Strand排序

## 平衡二叉树的各项操作时间复杂度都是O(logn)

在平衡二叉搜索树中，我们可以看到，其高度一般都良好地维持在O（log（n）），大大降低了操作的时间复杂度。一般都是的，但也能优化。看题目说得是不是绝对吧。

## 关于索引

能提升查询速率，这个是毋庸置疑的。

但是否能提升写入速率，这个就不一定的。因为每次写入还要更新一次索引，所以理论上是会降低写入速率的。

## String所指向的内存空间的内容

String对象不允许改变内容是**正确的**，但是可以改变定义的String类型的变量指向（看题目要求）。所以，这句话还是对的。

## Java IO用的两种设计模式

１、装饰者模式：在由InputStream，OutputStream，Reader和Writer代表的等级结构内部，有一些流处理器可以 对另一些流处理器起到装饰作用，形成新的，具有改善了的功能的流处理器。装饰者模式是Java I/O库的整体设计模式。这样的一个原则是符合装饰者模式的。

２、适配器模式：在由InputStream，OutputStream，Reader和Writer代表的等级结构内部，有一些流处理器是对其它类型的流源的适配。这就是适配器模式的应用。

## 抽象类和接口

1、抽象类和接口都不能直接实例化，如果要实例化，抽象类变量必须指向实现所有抽象方法的子类对象，接口变量必须指向实现所有接口方法的类对象。

2、**抽象类要被子类继承，接口要被类实现**。

3、**接口只能做方法申明，抽象类中可以做方法申明，也可以做方法实现**

4、接口里定义的变量只能是公共的静态的常量，抽象类中的变量是普通变量。

5、抽象类里的抽象方法必须全部被子类所实现，如果子类不能全部实现父类抽象方法，那么该子类只能是抽象类。同样，一个实现接口的时候，如不能全部实现接口方法，那么该类也只能为抽象类。

6、抽象方法只能申明，不能实现。abstract void abc();不能写成abstract void abc(){}。

7、抽象类里可以没有抽象方法

8、如果一个类里有抽象方法，那么这个类只能是抽象类

9、抽象方法要被实现，所以不能是静态的，也不能是私有的。

10、接口可以继承多个接口（Java里面唯一的多继承就在这），但类只能单根继承。

## 接口中的方法只能是抽象的吗

> 在传统版本上，接口中的所有方法必须是非静态的，且是abstract的，且是public的。普通方法可以不写修饰符，也会默认为public和abstract，当然你可以写上了。
>
> 但在java版本1.8中，这就不大一样了。
>
> 你可以为方法添加默认方法，这时候实现类不继承该方法也是可以编译通过的。
>
> 你还可以为接口添加静态方法。从技术角度来说，这是完全合法的。只是它看起来违反了接口作为一个抽象定义的理念。

比如Java8的Path接口

```
public interface Path {  
  public static Path get(String first, String... more) {  
  	return FileSystems.getDefault().getPath(first, more);  
  }  
}
```

## 反射：Class.forName用的是什么类加载器

一般会问是不是ExtClassLoader，其实不是。这个一般是用的类自己的类加载器。

## 类加载器

**系统提供的类加载器**

- 1.BootStarp(引导类加载器):负责加载[Java](http://lib.csdn.net/base/java)核心类库，不继承自ClassLoader加载器；
- 2.Extension(扩展类加载器)：负责加载java扩展库(例如sun公司专门为连接[数据库](http://lib.csdn.net/base/mysql)设计的JDBC的一组API)
- 3.Application(系统类加载器): 负责加载普通用户编写的java应用类

备注:

> 1.BootStrap加载器是由原生代码(C++)编写而成的，因此不继承自ClassLoader加载器，其他的类加载都必须继承自    ClassLoader加载器。
>
>  2.以上三个加载器是从表面理念上来说是父子关系，但事实并非继承关系，而是组合的关系

**自定义类加载器**

必须继承自ClassLoader加载器

例如:Tomcat里面的web加载器

## 乱七八糟的==

```
Integer i = 59;
System.out.println(i == Integer.valueOf("59"));
System.out.println(i == new Integer(59));
System.out.println(i.equals(Integer.parseInt("59")));
```

输出的结果是：

> true
> false
> true
>
> Process finished with exit code 0

## System.gc()是同步的吗

查了下源码

```
public static void gc() {
    Runtime.getRuntime().gc();
}
```

不是

## 重载和重写

**重写方法的规则**：

1、参数列表必须完全与被重写的方法相同，否则不能称其为重写而是重载。

2、返回的类型必须一直与被重写的方法的返回类型相同，否则不能称其为重写而是重载。

3、**访问修饰符的限制一定要大于被重写方法的访问修饰符**（public>protected>default>private）

4、重写方法一定不能抛出新的检查异常或者比被重写方法申明更加宽泛的检查型异常。例如：

父类的一个方法申明了一个检查异常IOException，在重写这个方法是就不能抛出Exception,只能抛出IOException的子类异常，可以抛出非检查异常。 

**而重载的规则：**

1、必须具有不同的参数列表；

2、可以有不责骂的返回类型，只要参数列表不同就可以了；

3、可以有不同的访问修饰符；

4、可以抛出不同的异常；

## 重写是 编译期绑定 判定调用哪个方法？

编译期绑定，简称动态绑定。运行期绑定又叫动态绑定。

Java中除了static和final方法（private方法属于final方法，因为类中的private方法被隐式指定为final方法,由此我们也可以知道：将方法声明为final类型的一是为了防止方法被覆盖，二是为了有效的关闭java中的动态绑定）之外，其他方法都是后期绑定。

## 类型转换

int + char的时候，char会转成int。也就是说int+char=int。

我的理解的话，为了防止内存溢出，所以你只要知道两个类型哪个大，结果类型就是哪个了。

## SQL语句 删表

drop Table xxx（表名）；记得要带上table。