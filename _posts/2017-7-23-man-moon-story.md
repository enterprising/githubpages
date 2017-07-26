---
layout: post
title: 《人月神话》-读书笔记
date: 2017-7-23 10:00
categories: 项目管理
tags: [项目管理]
top: true
---

* content
{:toc}
写在前面：

第一次知道这本书，是在大一的时候。当时是一个东南大学的学长推荐我去看的，不过说要等我有了一定的项目经验才去看。 人月管理主要是一本软件项目管理类的书籍，算是这方面的一本圣经了。

这本书非常适合反复读，所以本文会不定时的更新。第一遍只粗略地读，需要根据项目才能进行深入的理解。

每个小标题都是一个小节的标题名，没有改动。

## 焦油坑

​    讲了程序员的乐趣、苦恼。其实编程就是一个焦油坑，说不定你会困死在里面。然后作者说，本书的目的就是为了搭建一些桥梁，为我们这些通过焦油坑的人提供一些指导。

## 人月神话

​     缺乏合理的时间进度是造成项目滞后的最主要原因，它比其它所有因素加起来的影响还大。

​     人月其实并不是指的人和月亮。这么多年一直理解错了。

​     人月指的是 **工作量单位**。

​    brooks法则： 向进度落后的项目中增加人手，只会使得进度更加落后。

​    项目的时间依赖于顺序上的限制，人员的数量依赖于单个子任务的数量

## 外科医生队伍

​    给出了团队人员分配的一个建议。感觉分得很合理。

​	![屏幕快照 2017-07-23 下午4.55.59](https://ws3.sinaimg.cn/large/006tKfTcly1fhtxcai254j30lg0h4gmk.jpg)

## 贵族专制民主政治和系统设计

​    主要讲了概念的一致性，但是这种概念的定义设计怎么确定呢？这就是专制和民主的问题了。

​    概念的完整性要求设计必须由一个人或者非常少数互有默契的人员来实现。

​    那么在设计那种规范性的时间内，普通程序员岂不是无所事事？这个，当时是等设计好了再招人啊。

## 画蛇添足

​    项目经理如何避免画蛇添足？    

​        a、必须坚持至少拥有两个系统以上开发经验结构师的决定。

​        b. 必须对特殊诱惑的警觉，可以不断提出正确的问题，确保原则上的概念和目标在详细设计中得到完整的体现。

## 贯彻执行

​    如果一个项目经理已经拥有行事规范的架构师和许多编程人员，那么他如何确保每个人听从、理解并实现架构师的决策？

​       文档化的规格说明 - - - 手册、 形式化定义、 直接整合、 会议、多重实现、电话日志、产品测试

## 为什么巴比伦塔会失败

​    因为它们缺乏两个很重要的东西 ： 1、交流。  2、交流的结果  - - 组织

​    大型项目的展开类似于建造一个大型建筑。需要交流和组织。项目工作手册。

​    交流和交流的结果- - 组织，是成功的关键。交流和组织的技能需要管理者仔细考虑，相关经验的积累和能力的提高同软件技术本身一样重要。

## 胸有成竹

​    系统编程需要花费多长的时间？需要多少的工作量？如何进行估计？ 这一章主要就讲了这个问题，举了五个公司的例子。得出来一些结论。比如：对于常用编程语句而言，生产率似乎是固定的。 使用适当的高级语言，编程的生产率可以提高5倍。

## 削足适履

1、作为成本的程序空间

​	除了时间复杂度，空间复杂度也是重点。要注意控制空间复杂度。

2、规模控制

​	仅对核心程序设定规模目标的不够的，必须把所有的方便都编入预算。	 在指明模块有多大的同时，确切定义模块的功能。

3、空间技能

​	空间预算的多少和控制并不能使程序规模减小，为了实现这一目标，它还需要一些创造性和技能。

​	a. 用功能交换尺寸。b. 考虑空间-时间的折中。

4、数据的表现形式是编程的根本

## 提纲挈领

英文原文是 The Documentary Hypothesis。文件的假设？？

所以本章主要是跟文件有关的内容

1、计算机产品的文档

​	目标、技术说明、进度、时间表、预算、组织机构图、工作空间的分配、报价,预测和价格

2、大学里面每个系的文档

​	这个就不是重点了

3、软件项目的文档

​	做什么：目标。定义了待完成的目标、迫切需要的资源、约束和优先级。

​	做什么：产品技术说明。以建议书开始，以用户手册和内部文档结束。速度和空间说明是关键的部分。

​	时间：进度表

​	资金：预算

​	地点：工作空间分配

​	人员安排：组织图

4、为什么要有正式的文档

​	a.可以使目标变清晰。b.文档能够作为同其他人的沟通渠道。c.能让项目经理清楚项目目前的状态，以及哪些需要重点进行更改和调整。

## 未雨绸缪

不变的只是愿望，变化才是永恒 。

1、实验性工厂和增大规模

​	首先解释啥是实验性工厂，化学工程师很早就认识到，在实验室可以进行的反应过程，并不能在工厂中一步实现，一个被称为“实验性工厂”的中间步骤是很有必要的。

2、唯一不变的就是变化本身

​	目标上的一些变化无法避免，设计策略和技术上的变化也不可避免。抛弃原型概念本身就是对事实的接受。要学会 随着学习的过程更改设计。

3、为变更计划系统

​	使用高级语言和自文档技术，以减少变更引起的错误。变更的阶段化也是一种必要的技术，每个产品都应该有数字版本号和发布日程表以及冻结日期等。

4、为变更计划组织架构

​	“外科手术队伍”的设计，为了最小化成员间的接口，同样的，使系统在最大程度上易于修改。

5、前进两步，后退一步

​	程序维护。

![屏幕快照 2017-07-23 下午11.38.36](https://ws1.sinaimg.cn/large/006tKfTcly1fhu8u8lj8cj30jo0icgmc.jpg)

6、前进一步，后退一步

​	系统软件开发是减少混乱度(减少熵)的过程,所以它本身是处于亚稳态的。软件维护是提高混乱度(增加熵)的过程,即使是最熟练的软件维护工作,也只是放缓了系统退化到非稳态的进程。

## 干将莫邪

Sharp Tolls : 神器

可能有些人有自己收藏的一套工具，但是这对软件项目来说是非常愚蠢的行为。项目的关键是沟通，个性化的工具妨碍沟通。其次，工具这东西发展地非常快，所以，开发和维护公共的编程工具的效率更高。

建议每个团队有一名工具管理人员，管理所有通用工具，并且能够指导人们使用。

具体的工具的话，大概讲了这么几类。目标机器、辅助机器和数据服务、高级语言和交互式编程。

## 整体部分

这里主要讲的就是如何开发一个可以运行的系统，如何测试系统，如何将经过测试的一系列构件继承到已测试过、可以依赖的系统。

一、剔除bug的设计

a. 防范bug的定义。 要bug少，关键的工作是产品定义。细致的功能定义、详细的规格说明、规范化的功能描述说明以及这些方法的实施，大大减少了系统中必须查找的bug数量。

b. 测试规格说明。在编写任何代码之前，规格说明必须提交给测试小姑，以详细地检查说明的完整性和明确性。

c. 自顶向下的设计。逐步精化步骤。

d. 结构化编程。关键的地方和构建无bug程序的核心，就是把系统的结构作为控制结构来考虑，而不是独立的跳转语句。这种思考方法很有帮助。

二、构件单元测试

本机调试、内存转储、快照、交互式调试、测试用例

三、系统集成调试

1、使用经过调试的构建单元。

​	系统集成测试要求在每个部分都能正常运行之后才能开始。

2、搭建充分的测试平台

​	这里说的辅助测试平台，指的是供调试使用的所有程序和数据，它们不会整合到最终产品中。一种测试辅助的形式和伪构件、另一种形式是微缩文件。

3、控制变更	

​	对测试期间进行严密控制是软件和硬件调试中一项非常好的技术。

4、一次添加一个构件

​	这个就不解释了吧，很明显了

5、阶段（量子）化、定期变更

## 祸起萧墙

成语的一次是内部发生祸害或错误。

主要就是讲项目进度落后啥的。

一、里程碑还是沉重的负担？

​	制定进度表。表上每一件事被称为“里程碑”。

​	好的里程碑对团队来说实际上是一项服务，而不确切的里程碑是难以处理的负担。 里程碑必须是具体的、特定的、可度量的时间，能够进行清晰定义。

二、其他部分反正会落后

 	这种心理是不可取的。那么如何判断哪些偏离是关键的呢？ 此阿勇PERT或者关键路径技术。

​	随着项目的推进,PERT 图为前面那个泄气的借口,“其他的部分反正会落后”,提供了答案。它展示某人为了使自己的工作远离关键路径,需要超前多少,也建议了补偿其他部分失去的时间的方法。

三、地毯的下面

​	这个标题的意思是，出现计划偏离的时候，一线的负责人选择将这种污垢隐藏在地毯之下，不向上级报告。这种做法是不可取的。

​	有两种掀开毯子把污垢展现在老板面前的方法,它们必须都被采用。一种是减少角色 冲突和鼓励状态共享,另一种是猛地拉开地毯。

四、总结

​	对计划和控制职能进行适度的技术人力投资是非常值得赞赏的。它对项目的贡献方式 和直接开发软件产品有很大的不同。计划和控制小组作为监督人员,明白地指出了不易察觉 的延迟,并强调关键的因素。他们是早期预警系统,防止项目以一次一天的方式落后一年。

## 另一方面

wtf.. 这什么鬼标题

计算机程序是从人传递到机器的一些信息。为了将人的意图清晰地传达给不会说话的
机器,程序采用了**严格的语法和严谨的定义**。

一、需要什么样的文档

不同的用户需要的文档是不同的。

**使用程序：**

需要，目的、环境、范围、实现功能和使用的算法、输入输出格式、操作指令、选项、运行时间、精度和校验

**验证程序：**

需要，测试用例、数据边界（最大最小和特殊情形）的用例、确保无效的输入有正确的数据诊断提示

**修改程序：**

需要，流程图或子系统的结构图、对蒜用算法的完整描述、对所有文件规划的解释、数据流的概要操作、一些意见啥的比如对已预见修改的讨论等。

二、流程图

这个不细说了，应该还算比较熟悉了

三、自文档化的程序

​	自文档化方法激发了高级语言的使用,特别是用于在线系统的高级语言——无论是对
批处理还是交互式,它都表现出最强的功效和应用的理由。

## 银弹

这个是重点，简单是说就是要把最好的精力和工具放在最难攻克的项目难点上。