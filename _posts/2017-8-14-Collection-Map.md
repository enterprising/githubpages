---
layout: post
title: 容器深入研究
date: 2017-8-14 20:00
categories: Java
tags: [Java]
top: true
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

```java
public class SimpleHashMap<K, V> extends AbstractMap<K, V> {
    // Choose a prime number for the hash table
    // size, to achieve a uniform distribution:
    static final int SIZE = 997;
    // You can't have a physical array of generics,
    // but you can upcast to one:
    @SuppressWarnings("unchecked")
    LinkedList<MapEntry<K, V>>[] buckets =
            new LinkedList[SIZE];

    public V put(K key, V value) {
        V oldValue = null;
        int index = Math.abs(key.hashCode()) % SIZE;
        if (buckets[index] == null)
            buckets[index] = new LinkedList<MapEntry<K, V>>();
        LinkedList<MapEntry<K, V>> bucket = buckets[index];
        MapEntry<K, V> pair = new MapEntry<K, V>(key, value);
        boolean found = false;
        ListIterator<MapEntry<K, V>> it = bucket.listIterator();
        while (it.hasNext()) {
            MapEntry<K, V> iPair = it.next();
            if (iPair.getKey().equals(key)) {
                oldValue = iPair.getValue();
                it.set(pair); // Replace old with new
                found = true;
                break;
            }
        }
        if (!found)
            buckets[index].add(pair);
        return oldValue;
    }

    public V get(Object key) {
        int index = Math.abs(key.hashCode()) % SIZE;
        if (buckets[index] == null) return null;
        for (MapEntry<K, V> iPair : buckets[index])
            if (iPair.getKey().equals(key))
                return iPair.getValue();
        return null;
    }

    public Set<Entry<K, V>> entrySet() {
        Set<Entry<K, V>> set = new HashSet<Entry<K, V>>();
        for (LinkedList<MapEntry<K, V>> bucket : buckets) {
            if (bucket == null) continue;
            for (MapEntry<K, V> mpair : bucket)
                set.add(mpair);
        }
        return set;
    }

    public static void main(String[] args) {
        SimpleHashMap<String, String> m =
                new SimpleHashMap<String, String>();
        m.putAll(Countries.capitals(25));
        System.out.println(m);
        System.out.println(m.get("ERITREA"));
        System.out.println(m.entrySet());
    }
}
```

摘抄自书上的demo。。这个可以说是非常经典了。

里面有个非常重要的技术点。散列表中的“槽位”通常称为 桶位 （bucket）。为了使散列分布均匀，桶的数量通常使用质数.. 然而，事实证明，质数并不理想。因为，取余运算开销过大，所以一般使用 2 的整数次方当长度，然后用掩码代替除法，直接把取余的开销给消除掉了。

## 覆盖hashCode()

难怪上面那个代码看着怪怪的.. 就知道不是直接对SIZE取模嘛。

bucket数组的下标值的产生，依赖于具体的HashMap对象的容量，而容量的改变又与容器的 **充满程度** 和 **负载因子** 有关。hashCode()生成的结果，经过处理后成为bucket的下标。

hashCode() 最重要的一点就是：无论何时，对同一个对象调用hashCode()都应该生成同样的值。

在Effective Java Programming Language Guide 这本书里面，Joshua Bloch 给出了一份hashCode()建议模板。

![屏幕快照 2017-08-17 下午6.04.32](https://ws3.sinaimg.cn/large/006tKfTcgy1fimvoks3j8j30vs0hl76z.jpg)

代码：

```java
public class CountedString {
    private static List<String> created =
            new ArrayList<String>();
    private String s;
    private int id = 0;
    public CountedString(String str) {
        s = str;
        created.add(s);
        // id is the total number of instances
        // of this string in use by CountedString:
        for (String s2 : created)
            if (s2.equals(s))
                id++;
    }
    public String toString() {
        return "String: " + s + " id: " + id +
                " hashCode(): " + hashCode();
    }
    public int hashCode() {
        // The very simple approach:
        // return s.hashCode() * id;
        // Using Joshua Bloch's recipe:
        int result = 17;
        result = 37 * result + s.hashCode();
        result = 37 * result + id;
        return result;
    }
    public boolean equals(Object o) {
        return o instanceof CountedString &&
                s.equals(((CountedString) o).s) &&
                id == ((CountedString) o).id;
    }
    public static void main(String[] args) {
        Map<CountedString, Integer> map =
                new HashMap<CountedString, Integer>();
        CountedString[] cs = new CountedString[5];
        for (int i = 0; i < cs.length; i++) {
            cs[i] = new CountedString("hi");
            map.put(cs[i], i); // Autobox int -> Integer
        }
        print(map);
        for (CountedString cstring : cs) {
            print("Looking up " + cstring);
            print(map.get(cstring));
        }
    }
}
```

还有一点，重写hashCode()的时候，equals()也要注意一下。

<br/>

# 选择接口的不同实现

主要有两大接口：Collection和Map，五种非常常用的集合类：List、Queue、Set、HashMap和TreeMap。前三个是实现的Collection接口。

有三个是过时的，最好不要用：Hashtable、Vector和Stack。Hashtable是线程安全的.. 但太烂了。HashMap不是，如果想要线程安全，那么可以使用 currentHashMap。

ArrayList和LinkedList...  这个不说了，每次都是这个，要吐了。

说一下Set的三个实现吧，TreeSet、HashSet和LinkedHashSet。HashSet是最常用的，查询的速度最快；LinkedHashSet保持元素的插入顺序；TreeSet基于TreeMap，生成一个总是处于排序状态的Set。

按照自己的需要，选择合适的容器。

## 性能测试框架

.. 作者写了个demo，专门测试各种集合类的性能。计算其所有操作所需要的纳秒数。具体代码去我GitHub上面翻。当然，网上肯定还有更好的。

## 对List的选择

直接看测试结果

```java
--- Array as List ---
 size     get     set
   10      22      24
  100      18      27
 1000      12      18
10000      13      16
--------------------- ArrayList ---------------------
 size     add     get     set iteradd  insert  remove
   10     238      18      17      32     279     180
  100      25      25      23      93    2194     117
 1000      41      20      17      83     341     127
10000      15      15      17     539    1440     550
--------------------- LinkedList ---------------------
 size     add     get     set iteradd  insert  remove
   10      83      30      31      58     767     187
  100      14      46      46      28      93      26
 1000      22     420     424      11      66      16
10000      38    4886    4967      92      93      16
----------------------- Vector -----------------------
 size     add     get     set iteradd  insert  remove
   10     115      15      18      32     279      61
  100      10      14      23      27     292      30
 1000      10      14      16      63     191      84
10000      10      13      15     491    1441     576
-------------------- Queue tests --------------------
 size    addFirst     addLast     rmFirst      rmLast
   10          86          60          80          88
  100          21          16          15          16
 1000          22          41          38          37
10000          23          14          21          18
```

将ArrayList作为默认首选，其它看情况选择。插入删除的情况过多的时候，才考虑LinkedList。

## 微基准测试的危险

简单的说就是，防止JVM爆掉，要注意。注意死循环，和边界条件。

## 对Set的选择

```java
------------- TreeSet -------------
 size       add  contains   iterate
   10       511       133        58
  100       100        53         9
 1000        84        73         6
10000        80        62         6
------------- HashSet -------------
 size       add  contains   iterate
   10       152       128        48
  100        18         3         8
 1000        14         6         8
10000        15         6         5
---------- LinkedHashSet ----------
 size       add  contains   iterate
   10       396        67        25
  100        39        15         9
 1000        54        26         9
10000        27        22         9
```

HashSet的性能总是要比TreeSet好，特别是添加和查询元素的时候。

TreeSet存在的唯一卖点就是：它可以维持元素的排序状态，所以，只有需要一个排好序的Set的时候，才应该使用TreeSet。迭代也是一个优势，TreeSet的迭代通常要比HashSet要快。

另外LinkedHashSet的插入操作，代价比HashSet要高，因为它需要维护一个链表所带来的额外开销。

## Map的选择

```java
---------- TreeMap ----------
 size     put     get iterate
   10     452     100      42
  100     132      32       7
 1000      93      67       8
10000      88      63       9
---------- HashMap ----------
 size     put     get iterate
   10     314     126      47
  100      15       3       9
 1000      13       6       5
10000      17       9       8
------- LinkedHashMap -------
 size     put     get iterate
   10     173      56      19
  100      37      11       7
 1000      44      13       7
10000      29      13       7
------ IdentityHashMap ------
 size     put     get iterate
   10      89      35      24
  100      21      40      15
 1000      93      94      17
10000     101     101      19
-------- WeakHashMap --------
 size     put     get iterate
   10     122      35      21
  100      31       9      12
 1000      33      12      16
10000      28       9      17
--------- Hashtable ---------
 size     put     get iterate
   10      94      36      27
  100      33      21       9
 1000      39      26       9
10000      29      25      10
```

除了IdentityHashMap，所有的Map实现的插入操作都会随着Map尺寸的变大而明显变慢。但查找还是挺快的。

Hashtable的性能和HashMap差不多，但是前者线程安全，后者非线程安全。但.. 前面那个已经过时了。它们的底层的存储和查找机制是相同的，所以性能才差不多。HashMap是Hashtable的替代品。

TreeMap比HashMap要慢一点，嗯，你可以认为所有的hash开头的都比tree开头的要快。一般用HashMap，只有当Map始终保持有序的时候才需要使用TreeMap。

LinkedHashMap在插入的时候比HashMap要慢一点，但是它可以维持元素的插入顺序。嗯，内部实现的话是通过一个链表维护的。

IdentityHashMap，这是个奇葩。使用 == 来比较元素，不使用equals()。

… 可以手动调整HashMap来提高性能。

负载因子 = 尺寸/容量。 尺寸是指表中当前存储的项数，容量是指桶的容量。空表的负载因子为0，半慢表的负载因子是0.5。

HashMap和HashSet都允许使用者指定负载因子的构造器，当负载情况达到负载因子的水平时，容量将会自动增加（桶位数）。实现方式是让容量大致加倍，并将现有对象分布到新的桶位集中。（这叫做，再散列）

HashMap的默认负载因子是0.75

<br/>

# 实用方法

最强大的还是官方API

## List的排序和查询

用 binarySearch()，嗯，二分查找

shuffle()。这个方法是用来打乱List的顺序。

## 设定Collection或Map为不可修改

只读容器

> Collection<String> c = Collections.unmodifiableCollection(new ArrayList<String>(data));

## Collection或Map的同步控制

synchronized..

ConcurrentHashMap、Hashtable、阻塞队列（BlockingQueue）、Vector。

<br/>

# 持有对象

对象是可获得的，是指对象可在程序中的某处找到，这意味着你在栈中有一个普通的引用，而它正指向此对象。

如果一个对象是“可获得的”，垃圾回收器就不能释放它，因为它仍然为你的程序所用。

如果一个对象不是“可获得的”，垃圾回收期就能回收。

如果想继续持有对某个对象的引用，希望以后还能访问到该对象，但是也希望能够允许垃圾回收器释放它，这时候就需要使用Reference对象。

Reference对象... 嗯，代理模式。可以作为你和普通引用之间的代理。

## WeakHashMap

非常特殊的Map、用来保存WeakReference。它使得规范映射更易于使用。

在这种映射中，每个值只保存一份实例以节省存储空间。当程序需要那个”值“的时候，变在映射中查询现有的对象，然后使用它。

.. 所以，WeakHashMap，**允许垃圾回收器自动清理键和值**。允许清理元素的触发条件是：不再需要次键了。

<br/>

# Java 1.0/1.1的容器

老古董了.. 基本上都被遗弃了

Vector、Hashtable、Stack、BitSet.. 过时了，就不花精力去研究了。

<br/>

# 总结

hashCode() 是最大的亮点，Hash的实现方式可以说是非常机智了。其它东西的话，都还不错，然后，有需要的话，多查官方API。