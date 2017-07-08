---
layout: post
title: MainTask version 3
date: 2017-7-8 14:00
categories: DAL
tags: [DAL]
---

写在前面：

1、之前的思路已经比较清晰了，但！！太复杂了

2、代码越短越好！！！！这个是最最重要的

3、还是要多刷算法题，看看别人是怎么写代码的

4、项目里，那些命名要以 意图 为首选

5、不能出现 数字类的 flag

6、一般不能有remove之类的，如何涉及到移除，首选栈或者队列，不然很可能出现程序崩溃的情况

7、递归尽量少用，除非能让代码少三分之二

核心代码：

```java
public static GrepUtil grepUtil = new GrepUtil();
    public String startTime;
    public String endTime;
    public List<Host> hosts;
    public LinkedList<Group> groupQueue;
    public static LinkedList<Machine> machineLowQueue;
    public int max;
    /**
     * 获取当前可以调用的 host
     */
    @Test
    public void getFreeHost() {
        String hostName = "xg-dal-c6-22";
        //获取时间节点
        getTime();
        //获取所有的 host 信息
        hosts = grepUtil.gethosts(hostName, startTime, endTime);
	   //获取该主机上所有的 Group 信息。 这部分换成Queue会好很多
        groupQueue = grepUtil.getGroupQueueByHost(hostName, startTime, endTime);
        // 只需要获取 负载量最低的 host就行了，为了考虑时间复杂度，所以这里只考虑10个host
        machineLowQueue = getMachines(hosts, startTime, endTime, 10);
        // version1 用没挂掉的所有host里面最大的那台的host，当一个阈值
        max = hosts.get(hosts.size() - 1).getCount();
        //核心方法。
        moveMethodCore(groupQueue, machineLowQueue);
    }

    /**
     * 核心方法
     *
     * @param groupQueue      待移动的Group队列
     * @param machineLowQueue 可以接受Group的机器队列
     */
    private void moveMethodCore(LinkedList<Group> groupQueue, LinkedList<Machine> machineLowQueue) {

        LinkedList<Group> tempSubGroupQueue;  //待移动的子队列

        while (true) {
            if (groupQueue.isEmpty()) {
                System.out.println("\n结束了！\n");
                return;
            }
            if (machineLowQueue.isEmpty()) {
                //进入策略2、这时候机器列表为空，无法再移动了
            }
            //筛选出一批 group
            Machine targetMachine = machineLowQueue.peek();
            tempSubGroupQueue = searchQueues(groupQueue, targetMachine);    //这里是指 tempSubGroupQueue上的所有Group移动到targetMachine上面
            groupQueue.removeAll(tempSubGroupQueue);
            machineLowQueue.poll();
        }

    }

    /**
     * 该方法用于筛出来 groupQueue里面可以放在targetMachine上的最多的 group集合
     *
     * @param groupQueue    待移队列
     * @param targetMachine 目标机器
     * @return 返回一个group集合
     */
    private LinkedList<Group> searchQueues(LinkedList<Group> groupQueue, Machine targetMachine) {
        LinkedList<Group> groupQueue_temp = new LinkedList<>(groupQueue);
        int tempCount = targetMachine.getHost().getCount();
        LinkedList<Group> subGroupQueues = new LinkedList<>();

        //先筛不在group里面的那些
        Group group = groupQueue_temp.peek();
        while (!groupQueue_temp.isEmpty() && tempCount + group.getCount() < max) {
            group = groupQueue_temp.poll();
            if (isGroupInThisHost(group, targetMachine)) {
                System.out.println(group.getGroupName() + " 已经存在了");
                continue;
            }
            if (group.getCount() + tempCount <= max) {
                subGroupQueues.offer(group);
                tempCount += group.getCount();
            }
        }
        System.out.println(subGroupQueues.size());
        return subGroupQueues;
    }
```

