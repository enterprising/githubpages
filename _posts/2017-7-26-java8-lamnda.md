---
layout: post
title: 《Java8函数式编程》-读书笔记
date: 2017-7-26 16:00
categories: Java
tags: [Java,Java8]
---

* content
{:toc}
# 简介

1、面向对象编程是对数据进行抽象，而函数式编程是对行为的抽象。

​    现实世界中，数据和行为并存，程序也是如此，所以很有必要学习。

2、使得代码变得简单易读，易读就代表着业务逻辑清晰，也就代表着出错率会低。

3、函数式编程的核心在于：在思考问题时，使用不可变值和函数，函数对一个值进行处理，映射成另一个值。

# Lambda表达式

关键词；函数式编程

两个例子：

1、foreach

```java
Arrays.asList( "a", "b", "d" ).forEach( e -> {
    System.out.print( e );
    System.out.print( e );
} );
```

lambda表达式可以引用类成员和局部变量，会将这些变量隐式的转变成final

String separator = ",";

Arrays.asList( "a", "b", "d" ).forEach(

​    ( String e ) -> System.out.print( e + separator ) );

2、comparator

Collections.sort(names, (first, second) -> first.length() - second.length());

将过滤器建换成了一个lambda函数，上述代码能做到按names列表内元素的长度进行排序

优点，不需要指定参数的类型

在Java之前的传递行为：唯一的方法就是匿名内部类

比如：

```java
sendEmail(new Runnable() {
  @Override
  public void run() {
    System.out.println("Sending email...");
  }
});
```

使用lambda表达式之后：**sendEmail(() -> System.out.println("Sending email..."));**

**（）代表无参函数**

lambda表达式在Java8中的运行机制

你可能已经发现lambda表达式的类型是一些类似上例中的comparator的接口。但并不是所有接口都可以使用lambda表达式。

只有那些仅仅只包含一个非实例化抽象方法的接口才能使用lambda表达式。这样的接口被称为函数式接口。

这时候可能会有个疑问，Java8的lambda表达式是否只是一个匿名内部类的语法糖或者函数式接口是如何被转换成字节码的？

其实并不是。

Java8不采用匿名内部类有两个原因：

1、性能影响。如果lambda表达式采用匿名内部类实现，那么每一个lambda表达式都会在磁盘上生成一个class文件。当jvm启动时，这些class文件会被加载进来，因为所有的class文件都需要在启动时加载并在使用前确认。这会导致jvm变慢。

2、向后的扩展性，如果使用匿名内部类的方式，那么这将限制lambda表达式的未来发展范围。

Java8的设计者采用的是Java7中新增的动态启用来延迟在运行时的加载策略。

原理：当Javac编译代码时，它会捕获代码中的lambda表达式并且生成一个动态启用的调用地址（称为lambda工厂）。当动态启用被调用时，就会向lambda表达式发生转换的地方返回一个函数式接口的实例。

匿名内部类 vs lambda （区别）

1、在匿名类中，this 指代的是匿名类本身，但是在lambda中，this指代的是lambda表达式所在的这个类。

2、lambda表达式的类型是由上下文决定的，而匿名类中必须在创建实例的时候明确指定。

# 流

目的，流使程序员得以站在更高的抽象层次上对集合进行操作

1、迭代器的使用，一般算是外部迭代。这样的缺点是 很难抽象出其它操作，此外，从本质上讲是一种串行化操作。

​    使用内部迭代：

​            long count = allArtists.stream().filter(artist -> artist.isFrom("London")).count();

2、stream 是用函数式编程方式在集合类上进行复杂操作的工具

3、惰性求值，只要返回的结果是stream那么就是惰性求值；

​     及早求值，如果返回值是另一个值或者为空，那么就是及早求值

​    使用这些操作的理想方式就是形成一个惰性求值的链，最后用一个及早求值返回想要的结果，这正是它的合理之处。

​    allArtists.stream() .filter(artist -> artist.isFrom("London"));

​    像filter这样只描述stream，最终不产生新集合的方法叫做 惰性求值 方法。

​    像count这种，最终会从stream产生值的方法叫做 及早求值 方法

​    整个过程类似于 创建者模式。创建者模式使用一系列操作设置属性和配置，最后调用一个build方法，这时对象才被真正创建。

​    为什么要区分惰性求值和及早求值？ 为了只迭代一次

4、collet(toList( )) 方法

5、map ： 将一个流中的值转换成另一个新的流

6、filter： 过滤

​    注意，这个过滤不是指的过滤走满足条件的返回剩下的。这里返回的是过滤出来的，满足条件里面的。

7、flatMap：flatMap可用stream替换值，然后将多个stream连接成一个stream

​    例子：假设有一个包含多个列表的流，希望能得到所有的序列：

​            Stream.of(asList(1, 2), asList(3, 4)).flatMap(numbers -> numbers.stream() ).collect(toList);

8、min和max

​    Track shortestTrack = tracks.stream().min(Comparator.comparing(track -> track.getLength())).get();

9、reduce 操作

​    reduce的意思是减少、归纳为

​    reduce可以实现从一组值中生成一个值。 count、min、nax都属于reduce操作    

​    使用reduce求和：

​    int count = Stream.of(1, 2, 4).reduce(0, (acc, element) -> acc + element);

10、整合操作  重点

​        如何将问题分解成 stream 操作。 

​       将思路往上面这些关键字靠

鼓励用户使用 Lambda 表达式获取值而不是变量。获取值 使用户更容易写出没有副作用的代码

在 Lambda 表达式中使用局部变量, 可以不使用 final 关键字,但局部变量在既成事实上必须是 final 的。

无论何时,将 Lambda 表达式传给 Stream 上的高阶函数,都应该尽量避免副作用。唯一的 例外是 forEach 方法,它是一个终结方法。

11、不建议将流操作分开，建议一步完成。

​    原因：分成多个步骤的话，可读性差、效率差，每一步都要对流及早求值，生成了新的集合。而且一大堆垃圾变量（中间变量），浪费空间。难以自动并行化处理。

12、高阶函数

​    高阶函数是指接收另外一个函数作为参数，或返回一个函数的函数。

​    很容易辩论什么是高阶函数，一般只要看函数签名就可以了。如果函数的参数列表里包含函数接口，或该函数返回一个函数接口，那么该函数就是高阶函数。

​    Stream接口中的几乎所有函数都是高阶函数。

13、正确使用lambda表达式

​    作用，1）使代码更清晰更简洁、2）使用户更容易写出没有副作用的代码，具体是指，使用Lambda表达式获取值而不是变量。

总结：

内部迭代将更多控制权交给了集合类。

和Iterator类似,Stream是一种内部迭代方式。

将Lambda表达式和Stream上的方法结合起来,可以完成很多常见的集合操作。

练习

1、常用流操作

- numbers.collet(Collectors.summingInt(a->a));
- numbers.colect(a->a.getxxx,a.getxxx);

# 类库

前面说了如何编写lambda表达式，现在说如何使用lambda表达式。

默认方法和接口的静态方法。

一、在代码中使用Lambda表达式

​    不同的函数接口有不同的方法。如果使用Predicate，就应该调用test方法，如果使用Function，就应该调用apply方法

二、基本类型

​    拆箱装箱，int基本类型，Integer包装类。

​    包装类又叫装箱类型，是对象。因此在内存红存在额外开销。比如：整型在内存中占用4字节，整型对象占用16字节。

​    数组更严重，整型数组中的每个元素只占用基本类型的内存，而整型对象数组中，每个元素都是内存中的一个指针，指向Java堆中的某个对象。最坏的情况，同样大小的数组，Integer[] 要比int[] 多占6倍的内存。

​    将基本类型转换成装箱类型，称为装箱。将装箱类型转换成基本类型，称为拆箱。两者都需要额外的内存开销。

​    为了减少开销，所以Stream类的某些方法对基本类型和装箱类型做了区分。处理之后，如果方法返回类型为基本类型，则在基本类型前加To，如ToLongFunction。如果参数是基本类型，则不加前缀只需类型名即可，如LongFunction。 如果高阶函数使用基本类型，则在操作后加后缀To再加基本类型，如 mapToLong。

三、重载解析

​    总而言之,Lambda 表达式作为参数时,其类型由它的目标类型推导得出,推导过程遵循 如下规则:

  如果只有一个可能的目标类型,由相应函数接口里的参数类型推导得出;  

  如果有多个可能的目标类型,由最具体的类型推导得出;

  如果有多个可能的目标类型且最具体的类型不明确,则需人为指定类型。

​    注意：不能重写默认方法里面的equals和hashCode方法，因为它们是Object类定义的。

四、@FunctionlInterface   

​     事实上，每个用作函数接口的接口都用过加上这个注释。

​    该注释会强制javac检查一个接口是否符合函数接口的标准。如果该注释添加给一个枚举类型、类或另一个注释，或接口包含不止一个抽象方法，javac就会报错。重构代码时，使用它能很容易发现问题。

五、二进制接口的兼容性

​    使用Java1到Java7编译的类库或应用，可以直接在Java8上运行。

​    新增方法，意味着会打破平衡。比如Java8的Collection接口增加了stream方法，这意味着所有实现了Collection接口的类都必须增加这个新方法。当然也不是加上就没事了，加上可能会无法通过编译。

​    要避免这个糟糕情况，则需要爱Java 8中添加新的语言特性：默认方法。

六、默认方法 default关键字

​    Collection 接口中增加了新的 stream 方法,如何能让 MyCustomList 类在不知道该方法的情况下通过编译?Java 8通过如下方法解决该问题:Collection接口告诉它所有的子类: “如果你没有实现 stream 方法,就使用我的吧。”接口中这样的方法叫作默认方法,在任何接口中,无论函数接口还是非函数接口,都可以使用该方法。

默认方法和子类：

​    增加默认方法主要是为了在接口上向后兼容，让类中重写方法的优先级高于默认方法能简化很多继承问题。

七、多重继承

​    主要是指接口的多重继承。

​    万一多个接口里面遇到名字相同的方法，鬼知道你调用的是哪一个。要指定的话，需要在类中实现。比如：

```java
public class MusicalCarriage
implements Cariage, Jukebox {
  @Override
  public String rock() {
      return Carriage.super.rock();
  }
}
```

增强了super的语法。可以指定具体的接口里面的方法了。

使用默认方法的三定律：

1、类胜于接口。如果在继承链中有方法体或者抽象的方法声明，那么可以忽略接口中定义的方法。

2、子类胜于父类。如果一个接口继承自另一个接口，且两个接口都定义了一个默认方法，那么子类中定义的方法胜出。

3、没有规则3，如果上面两条不适用，子类要么实现该方法，要么将该方法声明成抽象方法。

第一条是为了然代码向后兼容。

八、权衡

​    用接口还是抽象类呢，这个要根据具体问题具体情况进行权衡。

九、接口的静态方法

十、Optional

​    reduce方法返回optional对象。

​    optional是为核心类库新设计的一个数据类型，用来替换null值。

​    使用optional的目的：1、Optional对象鼓励程序员适时检查变量是否为空，以避免代码缺陷。2）它将一个类的API中可能为空的值文档化，这比阅读实现代码要简单很多。

​    当试图避免控制相关的缺陷，如未捕获的异常时，可以考虑一下是否可以使用 Optional对象。

十一、要点回顾

​    1、使用为基本类型定制的Lambda表达式和Stream，如IntStream可以明显提升系统性能。

​    2、默认方法是指接口中定义的包含方法体的方法，方法名有default关键字做前缀。

​    3、在一个值可能为空的建模情况下，使用Optional对象能代替使用null值

# 高级集合类和收集器

一、方法引用

​    就比如 Artist::getName。这种写法就叫方法引用。 标准语法是ClassName::methodName。（构造方法也可以这样写。xxx::new、 还可以用来创建数组：String[]::new ）

​    需要注意的是不需要加括号。而且凡是使用lambda表达式的地方就可以使用方法引用。

二、元素顺序

​    流中的元素是以何种顺序排序。

​    直观上看,流是有序的,因为流中的元素都是按顺序处理的。这种顺序称为出现顺序。出 现顺序的定义依赖于数据源和对流的操作。

​    如果在一个有序集合中创建一个流时，流中的元素就按出现顺序排序。如果集合本身就是无序的，那么生成的流也是无序的。比如Set。

​    foreach：

​    使用并行流时，forEach方法不能保证元素是按照顺序处理的，如果需要按照顺序处理，应该使用forEachOrdered方法。

三、使用收集器

1、转换成其它集合

​    stream.collect(toCollection(TreeSet::new));  这个目前已经熟练掌握了

2、转换成值

​    maxBy、minBy 找出最大最小的值。

​    然后求和、求平均值啥的都有，基本上也都用过。

3、数据分块

​    将一个集合分成两个集合。partitionBy(xxx)

比如：使用方法引用将艺术家组成的 Stream 分成乐队和独唱歌手两部分

```java
public Map<Boolean, List<Artist>> bandsAndSoloRef(Stream<Artist> artists) { 
  return artists.collect(partitioningBy(Artist::isSolo));
}
```

4、数据分组

​    和分块不同，可以使用任意值对数据分组。groupingBy(xxx) 这个也很熟练了。

​    类似于SQL的 group by

5、转换成字符串

​    map提取内容，collet:: joining合并。

 例如：使用流和收集器格式化艺术家姓名

```java
     String result =
         artists.stream()
                   .map(Artist::getName)
                   .collect(Collectors.joining(", ", "[", "]"));
```

6、组合收集器

​    就是将这些收集器方法组合运用，可以做到非常强大的效果。

7、重构和定制收集器

​    可以自己写收集器。

8、对收集器的归一化处理

​    虽然自己写一个收集器不难，但是还是不太建议。可以构建若干个集合对象，作为参数传递给领域内类的构造函数。

四、一些细节

- compute方法。。这个太熟悉了
- map的foreach方法，用Stream实现会很方便。不用再写 
- for(Map.Entry\<Artist, List\<Album>> entry : albumsByArtist.entrySet()) {xxx} 这样子的代码了

五、要点回顾

- 方法引用是一种引用方法的轻量级语法，形如ClassName::methodName
- 收集器可以用来计算流的最终值，是reduce方法的模拟
- Java8提供了手机多种容器类型的方式，同时允许用户自定义收集器

# 数据并行化

未完待续