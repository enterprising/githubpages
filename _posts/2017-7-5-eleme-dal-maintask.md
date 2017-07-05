---
layout: post
title: MainTask version 1
date: 2017-7-5 14:00
categories: DAL
tags: [DAL,Java]
---

实习的主要任务，节点预测，保护DAL，维持DAL稳定

## version 0 

### tips：

​    1、Group的移动，只动300K以上的 （移大不移小、而且可以设置）

​    2、流量随着时间变化，是实时的，这个需要考虑

​    3、二维展开，要考虑时间复杂度和内存消耗，单线程是不可行的

​    4、第一点里面的300k，根据的是5min之内的数据。如果统计的是1min，就要换成 60k 或者其它

### steps：

1、根据时间节点，获取 host 和 对应的 流量，并按对应的流量进行降序

​    将其排序，找出一个avg

​    URL："[http://trace-gw.elenet.me/api/query/dal?ql=timer.dal.dashboard.group%20(0)%20by%20hostName%20range(](http://trace-gw.elenet.me/api/query/dal?ql=timer.dal.dashboard.group%20(0)%20by%20hostName%20range()"+startTime+","+endTime+”)"

2、根据hostList ，二维展开，找出对应host上面的GroupList，GroupList也要 按照流量降序进行排序

​    URL："[http://trace-gw.elenet.me/api/query/dal?ql=timer.dal.dashboard.group%20%7BhostName=%22](http://trace-gw.elenet.me/api/query/dal?ql=timer.dal.dashboard.group%20%7BhostName=%22)"+(hostlist.get(0).getKey()+"")+"%22%7D(0)%20by%20group%20range("+startTime+","+endTime+")";

3、根据avg，将 负载高的host 上面的 高负载Group 移到 负载低的host 上。

​    具体实现：

​    left  指向  hostlist 的最左

​    right  指向  hostlist 的最右

```java
    while( left < right ){
         if( left.count > avg  && right.count < avg ) {
            while (group > 0.3 && left -> group 在right上 ){
                 group 往下移一个 
            }
            while( group > 0.3 && left->group不在right上 && (left-group>avg && right+group<avg) ){   //这里用短路与
                 left -= group; right += group;  //移动left上的 负载高的group ,到right上
                 group 往下移一个
                 if(group <0.3){
                      // left 上没有什么Group可以移动了，这时候，粗略计算，看看left和right哪个偏差大，就移动哪个
                     if( abs(left.count - avg) > abs(right.count - avg) )
                            right - -;   
                     if( abs(left.count - avg) < abs(right.count - avg) )
                            left ++;
                     else{
                            left ++ ;  right - -;
                      }
                 } 
            }
        }
        // 这里是为了简化操作，因为无法刚好移动到平均值，所以允许一定的误差（可设置）
        if( right.count < avg+0.3 || right.count > avg -0.3 )
                right - - ;
        if( left.count < avg + 0.3 || left.count > avg - 0.3)
                left ++ ;
    }
```

left的Group移到right上，限制条件：

​    1、right移完之后不能超过平均值，left移完之后不能小于平均值

​    2、right指向的主机上不能已经存在该Group

​    3、不能有死循环

4、定期将执行上述三个步骤

优化：

1、在查出Group的时候，就剔除 300k 以下的；

2、移动完之后，left上要删除Group

### 结果：

老大说不能搞这么复杂，要简化

## version 1

直接上代码

核心代码：

```java
/**
 * Created by peng.tan on 17/7/4.
 * 这个方法用于当主机挂了的时候
 */
public class GetHosts {
    public static GrepUtil grepUtil = new GrepUtil();

    /**
     * 获取当前可以调用的 host
     */
    @Test
    public void getFreeHost() {
        String hostName = "xg-dal-c6-22";
        Date date2 = new Date();

        long startdate = date2.getTime();

        long end = date2.getTime() - 30000;
        long s = date2.getTime() - 330000;
        Date start = new Date(s);
        SimpleDateFormat sdf = new SimpleDateFormat("%22yyyyMMdd%20HH:mm:ss%22");
        String startTime = sdf.format(start);
        String endTime = sdf.format(end);

        //获取所有的 host 信息
        List<Map.Entry<String, Integer>> hostlist = grepUtil.getHostList(startTime, endTime);

        //获取该主机上所有的 Group 信息
        List<Map.Entry<String, Integer>> grouplist = grepUtil.getGroupByHost(hostName, startTime, endTime);

        // 只需要获取 负载量最低的 host就行了，为了考虑时间复杂度，所以这里只考虑10个host
        List<List<Map.Entry<String, Integer>>> hostLow = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            List<Map.Entry<String, Integer>> grouplist2 = grepUtil.getGroupByHost(hostlist.get(i).getKey(), startTime, endTime);
            hostLow.add(grouplist2);
        }

        // 求出 avg
        int avg = 0;
        for (Map.Entry<String, Integer> hostmap :
                hostlist) {
            avg += hostmap.getValue();
        }
        avg = avg / hostlist.size();
        System.out.println("host上面的平均负载为：" + avg);
        System.out.println("hostLow.size()-------- " + hostLow.size());

        // 核心部分
        // 由于可能遇到 死循环 （最大的Group移到最小的host，导致移完之后超过avg ）所以第一次不考虑那么多
        int hostIndex = 0;
        while (hostlist.get(hostIndex).getValue() + grouplist.get(0).getValue() > avg) {
            if (!isGroupINhost(grouplist.get(0).getKey(), hostLow.get(hostIndex))) {
                System.out.println("\n");
                System.out.println(grouplist.get(0).getKey() + " : " + grouplist.get(0).getValue());
                System.out.println("avg  = " + avg);
                System.out.println("移完之后，加起来的流量为： " + (hostlist.get(hostIndex).getValue() + grouplist.get(0).getValue()));
                System.out.println("hostLow的size = " + hostLow.size());
                System.out.println("将 " + hostName + " 下的 " + grouplist.get(0).getKey() + " 移到 host: " + hostlist.get(hostIndex).getKey());
                hostlist.get(hostIndex).setValue(hostlist.get(hostIndex).getValue() + grouplist.get(0).getValue());
                grouplist.remove(0);
                hostlist.remove(hostIndex);
                hostLow.remove(hostIndex);
                hostIndex = 0;
            } else {
                System.out.println("在 " + hostlist.get(hostIndex).getKey() + " 里面已经有啦！");
                hostIndex++;
            }
        }
        // 直到 该挂了的host 上面所有的 Group全部移完。每次都是移动第一个
        while (grouplist.size() != 0) {
            System.out.println("\n");
            // 这个Group 的 groupName 和 负载
            String groupNameTemp = grouplist.get(0).getKey();
            int tps = grouplist.get(0).getValue();
            if (hostLow.size() > 1) {
                //当前host已经放不下了，所以在hostList里将本host移除。 移完之后，回到第一个
                if (hostlist.get(hostIndex).getValue() + grouplist.get(0).getValue() > avg) {
                    System.out.println("主机：" + hostlist.get(hostIndex).getKey() + " 已经满了，现在将其移除。");
                    hostlist.remove(hostIndex);
                    hostLow.remove(hostIndex);
                    hostIndex = 0;
                    System.out.println("\n");
                }
                if (isGroupINhost(groupNameTemp, hostLow.get(hostIndex))) {
                    System.out.println("当前要移的Group为 ：" + groupNameTemp);
                    System.out.println("在 " + hostlist.get(hostIndex).getKey() + " 里面已经有啦！");
                    System.out.println("hostIndex = " + hostIndex);
                    System.out.println("hostLow = " + hostLow.size());
                    hostIndex = hostIndex < hostLow.size() ? hostIndex + 1 : 0;
                    System.out.println("hostIndex = " + hostIndex);
                    continue;
                }
                if ((!isGroupINhost(groupNameTemp, hostLow.get(hostIndex))) && (hostlist.get(hostIndex).getValue() + tps < avg)) {
                    System.out.println(groupNameTemp + " : " + tps);
                    System.out.println("avg  = " + avg);
                    System.out.println("移完之后，加起来的流量为： " + (hostlist.get(hostIndex).getValue() + tps));
                    System.out.println("hostLow的size = " + hostLow.size());
                    System.out.println("将 " + hostName + " 下的 " + grouplist.get(0).getKey() + " 移到 host: " + hostlist.get(hostIndex).getKey());
                    hostlist.get(hostIndex).setValue(hostlist.get(hostIndex).getValue() + grouplist.get(0).getValue());
                    grouplist.remove(0);
                    hostIndex = 0;
                }
            } else if (hostLow.size() == 1) {  //只剩下一台host了，全移到这个上面。(这里还有可能会遇到移不了的情况)
                System.out.println("将 " + hostName + " 下的 " + grouplist.get(0).getKey() + " 移到 host: " + hostlist.get(hostIndex).getKey());
                hostlist.get(hostIndex).setValue(hostlist.get(hostIndex).getValue() + grouplist.get(0).getValue());
                grouplist.remove(0);
            }
        }
        Date enddate = new Date();
        long enddate2 = enddate.getTime();
        System.out.println("运行时间为：" + (enddate2 - startdate) + " 毫秒");
    }

    /**
     * 判断 该Group 是否在 主机B 上已经存在
     *
     * @param groupName
     * @param host
     * @return
     */
    public static boolean isGroupINhost(String groupName, List<Map.Entry<String, Integer>> host) {
        for (Map.Entry<String, Integer> map :
                host) {
            if (groupName.equals(map.getKey())) {
                return true;
            }
        }
        return false;
    }
}
```

调用的工具类：

```java
/**
 * Created by peng.tan on 17/7/4.
 * 工具类
 */
public class GrepUtil {
    public HttpUtil httpUtil = new HttpUtil();

    public List<Map.Entry<String, Integer>> getGroupByHost(String hostName, String startTime, String endTime) {
        //排序器
        Comparator<Map.Entry<String, Integer>> valueComparator = (o1, o2) -> o2.getValue() - o1.getValue();
        String groupInHostURL = "http://trace-gw.elenet.me/api/query/dal?ql=timer.dal.dashboard.group%20%7BhostName=%22" + hostName + "%22%7D(0)%20by%20group%20range(" + startTime + "," + endTime + ")";
        String groupResult = httpUtil.getResult(groupInHostURL);
        TreeMap groupmap = new TreeMap<String, Integer>();
        JSON_group json_group = JSON.parseObject(groupResult, JSON_group.class);
        for (me.ele.sample.test.group.GroupedPoints item :
                json_group.getGroupedPoints()) {
            groupmap.put(item.getGroupBy().getGroup(), item.getPoints().getSummary().getCount());
        }
        List<Map.Entry<String, Integer>> grouplist = new ArrayList<Map.Entry<String, Integer>>(groupmap.entrySet());
        Collections.sort(grouplist, valueComparator);
        return grouplist;
    }

    public List<Map.Entry<String, Integer>> getHostList(String startTime, String endTime) {
        String result = "";
        HttpUtil httpUtil = new HttpUtil();
        String url = "http://trace-gw.elenet.me/api/query/dal?ql=timer.dal.dashboard.group%20(0)%20by%20hostName%20range(" + startTime + "," + endTime + ")";
        result = httpUtil.getResult(url);
        JSON_host j = JSON.parseObject(result, JSON_host.class);
        TreeMap hostmap = new TreeMap<String, Integer>();
        for (GroupedPoints item :
                j.getGroupedPoints()) {
            if (item.getGroupBy().getHostName().contains("xg-dal-c6")) {
                hostmap.put(item.getGroupBy().getHostName(), Integer.parseInt(item.getPoint().getSummary().getCount() + ""));
            }
        }
        //按值排序, 升序，Java 8 新写法
        Comparator<Map.Entry<String, Integer>> valueComparator = Comparator.comparingInt(Map.Entry::getValue);
        //将Map转换成list进行排序
        List<Map.Entry<String, Integer>> hostlist = new ArrayList<Map.Entry<String, Integer>>(hostmap.entrySet());
        //使用Collection的排序
        Collections.sort(hostlist, valueComparator);
        for (Map.Entry<String, Integer> entry :
                hostlist) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
        System.out.println(hostlist.get(0).getValue());
        System.out.println(hostlist.get(hostlist.size() - 1).getValue());
        return hostlist;
    }
}
```

老大点评（17.7.5）：

1、核心思路没有问题

2、代码太长，需要 review 一下

3、边界值那里不能那样处理，应该分散到多台机器上

4、先移小的，大的放在最后处理，顺序要换一下