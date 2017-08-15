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

未完待续.. 上班时间到了