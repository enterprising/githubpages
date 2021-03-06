---
layout: post
title: 设计模式
date: 2017-7-19 19:00
categories: 设计模式
tags: [设计模式]
top: true
---

* content
{:toc} 
# 创建型模式

## 简单工厂模式

**Simple Factory** Pattern

要点在于，当你需要什么，只需要传入一个正确的参数，就可以获取你需要的对象，而不需要知道其创建细节。

最大的优点就是：实现了对象的创建和对象的实现分离。对象的创建交给工厂实现。

## 工厂方法模式

**Factory Method** Pattern

简单工厂模式的一种扩展。就是在工厂类的上面再加了一层，简单工厂就相当于只有一个店铺，而工厂方法就是开了很多分店，每家分店就相当简单工厂的总店。负责对象的创建。

优点：增加新的产品类时无需修改现有系统。

缺点：增加新产品的同时需要增加新的工厂，增加了复杂性

## 抽象工厂模式

**Abstract Factory** Pattern

更复杂的工厂模式。

提供了一个创建一系列相关或互相依赖对象的接口，而无需指定它们具体的类

## 创建者模式

**Builder** Pattern

用于创建一个 **很复杂** 的对象。

使用一系列操作设置属性和配置，最后调用一个build方法，这时对象才被真正创建。

创建者抽象类中，定义了对象的创建方法和返回方法。

允许用户只通过指定复杂对象的类型和内容就可以构建它们，不需要知道具体的实现细节。

应用场景：在很多游戏软件中，地图包括天空、地面、背景等组成部分，人物角色包括人体、服装、装备等组成部分，可以使用建造者模式对其进行设计，通过不同的具体建造者创建不同类型的地图或人物。

## 单例模式

**Singleton** Pattern

这个很好理解了，就是一个类只能有一个实例。但是关于线程问题所以很适合当面试题。

写过一篇详细的博客：<http://blog.tanpeng.net/2017/07/09/singleton/>

<br/>

# 结构型模式

## 适配器模式

**Adapter** Pattern

将一个类的**接口转换成**客户**期望的另一个接口**。

适配器让原本接口不兼容的类可以相互合作。 适配器的功能就是把一个不合适的接口转换成另一个合适的接口。

以手机充电器为例：手机充电器一般都是5V左右吧，咱天朝的家用交流电压220V，所以手机充电需要一个适配器（降压器）

> Mobile mobile = new Mobile();  
> V5Power v5Power = new V5PowerAdapter(new V200Power());  
> mobile.inputPower(v5Power);

## 桥接模式

**Bridge** Pattern

**将抽象部分与它的实现部分分离**，使它们都可以独立的变化。

主要用于两个维度的变化

桥接模式又叫接口模式。

举例说明：

> 设想如果要绘制矩形、圆形、椭圆、正方形，我们至少需要4个形状类，但是如果绘制的图形需要具有不同的颜色，如红色、绿色、蓝色等，此时至少有如下两种设计方案：  
>
> - 第一种设计方案是为每一种形状都提供一套各种颜色的版本。
> - 第二种设计方案是根据实际需要对形状和颜色进行组合
>
> 对于有两个变化维度（即两个变化的原因）的系统，采用方案二来进行设计系统中类的个数更少，且系统扩展更为方便。设计方案二即是桥接模式的应用。桥接模式将继承关系转换为关联关系，从而降低了类与类之间的耦合，减少了代码编写量。

再比如：

> 一个Java桌面软件总是带有所在操作系统的视感(LookAndFeel)，如果一个Java软件是在Unix系统上开发的，那么开发人员看到的是Motif用户界面的视感；在Windows上面使用这个系统的用户看到的是Windows用户界面的视感；而一个在Macintosh上面使用的用户看到的则是Macintosh用户界面的视感，Java语言是通过所谓的Peer架构做到这一点的。Java为AWT中的每一个GUI构件都提供了一个Peer构件，在AWT中的Peer架构就使用了桥接模式

## 装饰模式

**Decorator** Pattern

简单的说就是**动态的给一个对象添加额外的功能。**

即做到不修改原来的对象类，至少在原来的基础上加以装饰，添加部分新的功能或属性。

应用场景的话，就比如游戏里面，装备是一个类，宝石又是一个类（装饰类）。然后用宝石装饰原来的装备，这个就是装饰模式。动态的将功能加到了原来的装备类上，扩展了功能而且还不需要修改原来的装备类。这就是装饰模式。

## 外观模式

**Facade** Pattern

为系统向外界提供一个统一的接口，用来访问子系统的一群接口。**其实就是为了方便用户的使用，把一群操作封装成一个方法。**

应用场景的话，就比如：我比较喜欢看电影，于是买了投影仪、电脑、音响、设计了房间的灯光、买了爆米花机，然后我想看电影的时候，我需要**一键观影和一键关闭**。

然后我们把一系列的参数传进去，只需要调用一个方法就能做到一键操作。这就是外观模式。

## 代理模式

**Proxy** Pattern

翻墙就是代理模式的典型应用啊

对代理模式我还是挺有感情的，因为当时面试饿了么的时候，老大问过我这个模式。当时其实听都没听过，就根据 “代理” 这个词跟他扯了一大堆，最后好像还真是那个意思。为我拿到offer做出了不少贡献。

所谓的代理模式，其实就是： **封装被代理对象，并限制外界对代理对象的访问。**

简单来说的话其实就是个中间层，想在访问一些类的时候做一些控制的时候需要使用。

装饰模式和代理模式的区别：**装饰器模式关注于在一个对象上动态的添加方法，然而代理模式关注于控制对对象的访问**。就是，一个是为了新增东西，一个是为了控制访问权限。<http://www.cnblogs.com/jaredlam/archive/2011/11/08/2241089.html> 

**生活中的代理模式：**

> 我们购买火车票可以去火车站买，但是也可以去火车票代售处买，此处的火车票代售处就是火车站购票的代理，即我们在代售点发出买票请求，代售点会把请求发给火车站，火车站把购买成功响应发给代售点，代售点再告诉你。
> 但是代售点只能买票，不能退票，而火车站能买票也能退票，因此代理对象支持的操作可能和委托对象的操作有所不同。

**翻墙：**

> 远程代理：我们在国内因为GFW，所以不能访问 facebook，我们可以用翻墙（设置代理）的方法访问。访问过程是：  
> (1)用户把HTTP请求发给代理  
> (2)代理把HTTP请求发给web服务器  
> (3)web服务器把HTTP响应发给代理  
> (4)代理把HTTP响应发回给用户  

**Java动态代理的内部实现：**

Java 是怎么保证代理对象调用的任何方法都会调用 InvocationHandler 的 `invoke()`方法的呢？

这就涉及到动态代理的内部实现。顺带还解决了一个疑问：invoke方法传入的Ojbect proxy，这个proxy到底是什么，为什么调用proxy.invoke会出现无限递归？

假设有一个接口 Subject，且里面有 `int request(int i)` 方法，则生成的代理类大致如下：

```
public final class $Proxy1 extends Proxy implements Subject{
	private InvocationHandler h;
	private $Proxy1(){}
	public $Proxy1(InvocationHandler h){
		this.h = h;
	}
	public int request(int i){
		Method method = Subject.class.getMethod("request", new Class[]{int.class});	//创建method对象
		return (Integer)h.invoke(this, method, new Object[]{new Integer(i)}); //调用了invoke方法
	}
}
```

通过上面的方法就成功调用了 invoke() 方法。

<br/>

# 行为型模式

## 命令模式

**Command** Pattern

**将“请求”封装成对象，将动作请求者和动作执行者进行解耦。**

实例：电视机遥控器

> 电视机是请求的接收者，遥控器是请求的发送者，遥控器上面有一些按钮。不同的按钮对应电视机不同的操作。

## 中介者模式

**Mediator** Pattern

**用一个中介对象来封装一系列的对象交互**。

中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。

应用场景：**MVC架构中控制器**

Controller 作为一种中介者，它负责控制视图对象View和模型对象Model之间的交互。如在Struts中，Action就可以作为JSP页面与业务对象之间的中介者。

## 观察者模式

**Observer** Pattern

简单的说就是一个东西变化了，那么关注它的对象都会收到通知。应用场景的就像 微信公众号的关注、推送一样。

代码参考：

<https://github.com/enterprising/designPatterns/tree/master/src/observer>

## 状态模式

**State** Pattern

定义：**当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。**

换句话说就是：允许对象在内部状态发生改变时改变它的行为。

应用场景：状态模式在工作流或游戏等类型的软件中得以广泛使用，甚至可以用于这些系统的核心功能设计，如在政府OA办公系统中，一个批文的状态有多种：尚未办理；正在办理；正在批示；正在审核；已经完成等各种状态，而且批文状态不同时对批文的操作也有所差异。使用状态模式可以描述工作流对象（如批文）的状态转换以及不同状态下它所具有的行为。

## 策略模式

**Strategy** Pattern

定义一系列算法，将每一个算法封装起来，并让它们可以相互替换。

用一句话来说，就是“**准备一组算法，并将每一个算法封装起来，使得它们可以互换**”。

应用场景：比如Collections.sort(List list, Comparator c); 可以通过实现多个Comparator接口来达到多种排序的目的。

## 责任链模式

**Chain of Responsibility**

简单的说就是，很多对象由每个对象对其下家的引用而连接起来形成一条链。请求在这条链上传递，直到链上的某个对象决定处理此请求。

发出请求的客户端并不知道链上的哪个对象最终处理这个请求，这使得系统可以在不影响客户端的情况下动态地重新组织和分配任务。

现实中的场景，比如红楼梦里面的击鼓传花：

![下载](https://ws3.sinaimg.cn/large/006tKfTcgy1fiutwfwtmij30ff04q3yn.jpg)