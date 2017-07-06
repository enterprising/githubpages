---
layout: post
title: DAL代码质量说明文档
date: 2017-7-6 15:00
categories: DAL,Java
tags: [DAL,Java]
---



## 代码质量

------

### 代码实现后交付前的的CheckList

------

1. 如何在开发阶段验证所开发的功能？
   1. 正常功能
   2. 异常情况处理
2. 如何在运行系统上观察此功能的运行时状态？
   1. 启动时加载项和必要过程点
   2. 配置变更
   3. 如何动态抓取系统快照（这里的快照并不一点指进程的函数栈，可以是通过debug端口查看正在运行的请求状态，队列长度等各种动态信息）
   4. 错误日志是否提供足够的上下文分析问题
   5. 是否需要metrics/bell告警项

### 代码实现中的CheckList

------

1. 代码 if-else 类带分支的嵌套不宜超过**1层**，最多不要超过**2层**，

   ```java
   if() {
       if () {
            // no more if/else
       } else {
           // no more if/else 
       }
   } else {
       if () {
          // no more if/else
       }
   }
   ```

2. 其它较单一的嵌套不宜超过3层。

   ```java
   for () {
      for() {
         if () {
            // no more embeded blocks
         } 
      }
   }
   ```

3. 函数长度小于**50行**为宜，最多不要超过**150行**。类最多不宜超过**500行**。

4. 代码块（if/else/for)等前序 '**{**'  配对的  ‘**}**’ 应该肉眼易于分辨，如果要用IDE加亮方式寻找匹配项，应考虑将代码块重构简化。

5. 重要的业务规则，较长的复合判断条件（if ( x && y && z ），应该提取为判断函数。

6. 避免使用 null 语义，集合可以考虑返回空集合，字符串返加“”， 数字类是否有合理默认值。

7. 优先用**意图**命名，避免用**实现动作**命名，首先应考虑使用**业务领域**的词汇，其次参照经典算法、知名API词汇。举例如下，对于DAL配置项、代码模块间接口，命名时优先选择 **#a**， 其次**#b**，再次 **#c**：DAL用信号量控制的DB的并发请求数，业务领域的词汇是“**限流**” ，各产品、项目经理、运营等都可以讨论 “限流”的问题，不需要关注DAL是如何实现的。实现方式是 限制到DB的 "**并行度**“， 这一层还是概念模型层的，是通过“并行度”来限制，而不是通过“计算每秒请求数”。更低一层使用 “**信号量**” ， 这一层到实现层了，不是通过多线程阻塞的方式，而是用信号量资源计数的方式。

### 代码实现前的CheckList

------

1. 确认要求的功能行为已经明确，并且了解需求的动机和使用场景。

2. 将实现分解为可迭代的逻辑块/层​

   1. 可以明确的衡量代码实现进度（标识为已完成的部分应该是可验证的），而不是一个大的黑盒（要么完成，要么没完成）
   2. 如果开发量在3天以上，应该将代码实现方案分解为一系列可Review，可单元测试的模块（类，函数等）。

3. 尽量减少对原有代码和逻辑的侵入性

   1. 新功能实现尽量独立，原有逻辑中尽可能少的点调用新功能即可
   2. 增加新的逻辑层管理原来的功能和新功能
   3. 最小化更改原有代码逻辑，避免把重构遗留代码和新功能开发混一起

## 线上质量

------

1. 线上紧急处理所采取的临时措施，必须不间断制定计划恢复到标准行为。
2. 通过在wiki上准备发布日历和事件记录表的方式，全组知道并可查询最近的发布情况。
   1. 发布包括新版本上线和推送配置
3. 标准group增加或db变更支持组内应review
4. DAL服务自身参数据变更需要开发组review
   1. 比如 发布面板上的参数，dal线程数，sharding参数，globalId参数等

## 再看代码质量

------

最近代码review中发现的问题及应对。

### 重构

------

1.  低耦合：不同的功能都通过if/else分支穿插在一起，类越来越大，函数更多更长。应该遵循单一职责原则进行合理的划分。
2.  高内聚： 要改多处来实现一个功能，尽可能将一个功能的相关数据和操作在一个逻辑模块里完成。

​       — 从整个产品的解度了解代码全貌，相互交流代码，不要只埋头写自己的。

3. 重构，代码工作后，重新梳理流程，去掉不必要逻辑。

​      — 不要回忆原来是怎么做的，象用户一样再研究一遍流程。

4. 简洁，减少公共方法，一个模块就象一个服务一样，用尽可能少的接口完成工作。一个接口依赖另一个接口的合并掉，让用户调多个接口完成的合并为掉一个接口。

​     — 用一站式服务大厅的思路考虑问题。让我们跑好几个部门去解决我们自己的一个问题我们肯定也不高兴。

5. 简洁，去掉过小私有方法，（如果表达式能够反应出函数名一样的意图）

​     — 在抽象和实现的可见性上做平衡。

  6.简单，减少嵌套

   — 通过条件反转或抽出子深层嵌套部分为函数，尽可能写直线性代码