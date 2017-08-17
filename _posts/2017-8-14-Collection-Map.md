---
layout: post
title: 容器深入研究
date: 2017-8-14 20:00
categories: Java
tags: [Java]
---

* content
{:toc} 
内容整理自《Java编程思想》第四版，第17章。

深入研究，嗯，我喜欢这个词。

# 完整的容器分类法

![屏幕快照 2017-08-14 下午6.20.39](https://ws1.sinaimg.cn/large/006tNc79gy1fijfa9v6mrj30pt0n00wj.jpg)

嗯，这图有点复杂.. 还是先看简单点的吧，一步步的来。

![](http://upload-images.jianshu.io/upload_images/1936008-7cd7aa2528a30ba1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

… 关于那些基础概念。随便一个点我都能说上半天，唉，不说了，东西全在图里面。

有这么几点要说明一下（看第一张图）：

Java SE5新添加了：

- Queue接口及其实现PriorityQueue和各种风格的BlockingQueue
- ConcurrentMap接口及其实现ConcurrentHashMap，它们是用于多线程机制的。里面实现的话，由两部分组成，一个是Segment数组，一个是HashEntry、Segment是一种可重入锁，在里面担当锁的角色，HashEntry用来存数据。每当HashEntry数组的数据要进行修改的时候，必须首先获得它对应的Segment锁
- CopyOnWriteArrayList和CopyOnWriteArraySet，也是用于多线程的
- 在Collections类中的多个便利方法

# 填充容器

Collections.nCopies（）

Collections.fill（），相比上面那个，这个方法有限制，它只能替换已经在List中存在的元素，而不能添加新的元素。

一、一种Generator（生成器）解决方案

二、Map生成器

三、使用Abstract类

.. 说实话，我不觉得这部分有多重要，基本上没见过。

# Collection的功能方法

add、addAll、contains、isEmpty那些。

但是里面不包括随机访问所选择元素的 get() 方法。因为Collection包括Set，而Set是自己维护内部顺序的（这使得随机访问变得没有意义）。因此，如果想检查Collection中的元素，那么就必须使用**迭代器**。

# 可选操作

执行各种不同的添加和移除的方法在Collection接口中都是可选操作，这意味着实现类并不需要为这些方法提供功能定义。

里面提到了一种罕见的异常：UnsupportedOperationException（未获支持的调用）。

报这种错的时候，一般是对固定尺寸的数据结构进行了大小的改变。这当然不会让你改啊。 

Arrays.asList() 会生成一个list，它基于一个固定大小的数组，仅支持那些不会改变数组大小的操作.. so ，你一对它的尺寸进行修改，就会报错。

# List的功能方法

这里用的是一个例子说明。具体有哪些自己去查官方的API文档。

# Set和存储顺序

![屏幕快照 2017-08-15 上午9.53.42](https://ws3.sinaimg.cn/large/006tNc79gy1fik694al4oj30v206vwga.jpg)

HashSet上面打星号表示：如果没有其他的限制，这就应该是你默认的选择，因为它对速度进行了优化。

然后，还讲了一下重写hashcode和equals方法。书上说，对于良好的编程风格而言，你应该在覆盖equals方法的时候，同时覆盖hashCode方法。

里面还提到了，重写compareTo()方法的时候，直接写 return i-i2 ，这样是错误的。它室友在 i 和 i2 都是无符号的 int 时才能正确工作，对于有符号的 int ，它就会出错。

因为int不够大，不足以表现两个有符号 int 的差。例如 i 是很大的正整数，而 j 是很大的负整数，i - j 就会溢出并且返回负值，这就错了。

而且还有一点，我们通常希望compareTo()方法可以产生与equals()方法一致的自然排序。这是什么意思呢？就是说，如果equals()对于某个特定的比较返回TRUE，那么compareTo()就应该要返回0.如果equals()返回false，那么compareTo()就要返回非0的值。

最后再稍微总结上图里的三种最常用的Set。

- HashSet 以某种神秘顺序保存所有的元素。。 这个后面再说
- LinkedHashSet按照元素的插入顺序保存元素
- TreeSet按照排序顺序维护元素。（按照compareTo()的实现方式）

SortedSet

![屏幕快照 2017-08-15 上午10.14.46](https://ws2.sinaimg.cn/large/006tNc79gy1fik6uxqy5ej30vr0a6tbf.jpg)

稍微总结一下，SortedSet的意思是“按照对象的比较函数对元素排序”，而不是“元素插入的次序”。

插入顺序的话，可以用LinkedHashSet来保存。

<br/>

# 队列

除了并发应用，Queue在Java SE5中仅有的两个实现是：LinkedList和PriorityQueue，它们的差异在于排序行为而不是性能。

看个例子吧

![两种队列的比较](https://ws4.sinaimg.cn/large/006tKfTcgy1fiko98l7aqj30mc0gcwhe.jpg)

根据上面的运行结果，我们可以发现：除了优先级队列，其它的都是精确地按照元素被放置的顺序产生它们。

## 优先级队列

一个类继承PriorityQueue，重写里面的compareTo方法。就能根据自己想要的想过进行队列里面的优先级排序了。

## 双向队列

双向队列又叫双端队列，其实就是一种特殊的队列。但是你可以在任意一端添加或移除元素。

LinkedList中包含支持双向队列的方法，但是Java标准类库中目前（Java 5）没有任何显示的用于双向队列的接口。因此，LinkedList无法去实现那样的接口。

所以.. 字节写一个Deque类吧，直接从LinkedList中暴露方法。

这就看你数据结构理解的怎么样了。

<br/>

# 理解Map

Map的中文名叫做.. 地图，也叫做映射表或者关联数组。它的基本思想是维护键值对关联。因此你可以根据键来查找值。

用得比较多的Map有：HashMap、TreeMap、LinkedHashMap、WeakHashMap、ConcurrentHashMap、IdentityHashMap。它们有继承了Map接口，但是行为特性不同。不同表现在：效率、键值对的保存及呈现次序、对象的保存周期、映射表如何在多线程程序中工作和判定“键”等价的策略等方面。

可以说设计者想得非常周全。

## 性能

这里的性能主要表现在 get() 方法上面。如果是一般的线性搜索的话，执行的速度会非常慢。HashMap就比较厉害了。它使用了特殊的值，称为 **散列码** 。

散列码是“相对唯一”的，用以代表对象的int值，它是通过将该对象的某些信息进行转换而生成的。

hashCode()是根类Object中的方法，因此所有Java对象都能产生散列码。HashMap就是使用对象的 hashCode() 进行快速查询的，这种方法能够显著提高性能。

| Map大家族            | 特点                                       |
| ----------------- | :--------------------------------------- |
| HashMap           | 1、如果没有特殊要求也的话，这是默认实现方式，因为它快。             |
| LinkedHashMap     | 1、类似上面，但迭代遍历的时候它快一些。2、按照插入顺序或者LRU排序。     |
| TreeMap           | 1、基于红黑树实现。按照Comparator排序。2、是唯一一种可以返回字数的Map。 |
| WeakHashMap       | 弱键映射、允许释放映射所指向的对象。                       |
| ConcurrentHashMap | 1、线程安全。2、不涉及同步加锁，3、Segment和MapEntry.. 嗯，也是锁。 |
| IdentityHashMap   | 使用==代替equals()对 键 进行比较的散列映射。用于特殊情况。      |

## SortedMap

SortedMap（TreeMap是其现阶段的唯一实现.. 嗯现阶段是Java 5），可以确保键处于排序状态。

Java 7 有 ConcurrentSkipListMap, TreeMap、

里面排序有个非常核心的东西，就是comparator。

## LinkedHashMap

看到Linked就知道它和链表有关。一般和HashMap没啥区别，但它在内部维护了一个链表，用来记录元素的插入顺序。所以在遍历键值对的时候，能以元素的插入顺序输出。

<br/>

# 散列与散列码

.. 原来，这东西在书上已经讲过了。唉.. 

正确的equals()方法必须满足5个条件：

- 自反性。对于任意 x , x.equals(x) 放阿飞一定返回 true
- 对称性。对于任意的 x 和 y，如果y.equals(x)返回true，那么x.equals(y)也要返回true
- 传递性，对任意x 、y、 z，如果x.equals(y)返回true，y.equals(z)返回true，那么x.equlas(z)也一定返回true
- 一致性，对于任意的x和y，如果对象中用于等价比较的信息没有改变，那么无论调用x.equals(y)多少次。返回的结果应该保持一致，要么一直是true要么一直是false
- 对于任何不是null的x，x.equals(null)一定返回false

## 理解hashCode()

如果不为你的key覆盖hashCode()和equals()，那么使用散列的数据结构（HashSet、HashMap、LinkedHashSet或LinkedHashMap）就无法正确处理你的key。

使用散列的目的在于：使用一个对象来查找另一个对象。当然.. 散列只是Hash开头的，和一般的Map不一样。

还有一点，在equals()方法里面，要同时比较key和value。

## 为速度而散列

散列的价值在于速度：散列使得查询得以快速进行。

速度的瓶颈在于key的查询速度，所以.. 要保持键的排序状态，然后使用Collections.binarySearch()进行查询。

散列则更进一步，它将key保存在数组。（因为数组是最快的数据结构、当然保存的是key的信息，而不是key本身）。但数组的大小是固定的.. Map的容量又是不固定的，所以？ 数组并不保存key本身，而是通过key对象生成一个数组，这个数字就是数组的下标，同时也叫做 **散列码** ，散列码由hashCode()方法生成。

但是... 不同的键可能会产生相同的散列码，也就说，可能会有冲突。先不管冲突..  这种现象的产生解决了数组容量被固定的问题，也就是说.. 数组多大就不是重点了，任何键总能阿紫数组中找到它的位置。

通过key查询value的过程：

首先计算key的散列码，然后使用散列码查找数组。如果没冲突最好，有冲突的话，由外部链接处理：数组并不直接保存值，而是保存值的list。然后对list中的值使用equals()方法进行线性的查询（当然，线性查询很慢，但如果散列函数hashCode()足够好的话，数组的每个位置就只有较少的值）。因此，不是查询整个list，而是快速地跳到数组的某个位置，只对很少的元素进行比较。这就是HashMap的原理。。所以它非常快。

理解了原理，自己实现一个简单的散列Map：

> 。。未完待续