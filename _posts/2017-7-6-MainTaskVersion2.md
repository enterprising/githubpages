---
layout: post
title: MainTask version 2
date: 2017-7-6 20:00
categories: DAL
tags: [DAL]
---

说明：更换了一下处理顺序，先处理负载小的，再动负载大的。

收获：熟悉了两种Java8的新用法，一个是过滤器，一个是日期时间类。对数据结构中的队列能做到轻松熟练的使用。

核心代码：

```java
/**
 * Created by peng.tan on 17/7/6.
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

        LocalDateTime startPoint = LocalDateTime.now().minusSeconds(30);
        DateTimeFormatter dft = DateTimeFormatter.ofPattern("%22yyyyMMdd%20HH:mm:ss%22");
        String startTime = startPoint.format(dft).toString();
        LocalDateTime endPoint = startPoint.plusMinutes(5);
        String endTime = endPoint.format(dft).toString();
        System.out.println("开始时间为：" + startTime);
        System.out.println("结束时间为：" + endTime);

        //获取所有的 host 信息
        List<Host> hosts = grepUtil.gethosts(hostName, startTime, endTime);// add class for key/value

        //获取该主机上所有的 Group 信息。 这部分换成Queue会好很多
        LinkedList<Group> groupQueue = grepUtil.getGroupQueueByHost(hostName, startTime, endTime);

        // 只需要获取 负载量最低的 host就行了，为了考虑时间复杂度，所以这里只考虑10个host
        LinkedList<HostGroups> hostLowQueue = new LinkedList<>();
        for (int i = 4; i < 14; i++) {
            List<Group> grouplistTemp = grepUtil.getGroupByHost(hosts.get(i).getHostName(), startTime, endTime);
            HostGroups hostGroups = new HostGroups();
            hostGroups.setHost(hosts.get(i));
            hostGroups.setGroupList(grouplistTemp);
            hostLowQueue.add(hostGroups);
        }

        // version1 用没挂掉的所有host里面最大的那台的host，当一个阈值
        int max = hosts.get(hosts.size() - 1).getCount();
        System.out.println("目前的阈值为：" + max + "\n");

        //直到所有Group全都移走为止
        while (!groupQueue.isEmpty() && !hostLowQueue.isEmpty()) {
            Group group = groupQueue.peek();
            HostGroups targetMachine = hostLowQueue.peek();

            //如果该机器上已经存在了该Group。处理方法：将GroupQueue轮转，弹出一台，放在队尾
            //隐藏问题，所有的Group都在该 machine 上有了，就会存在死循环。 解决方法： 设置一个标志值，用于记录循环次数。当该值超过队列长度的时候，换机器。
            int groupFlag = 0;
            while (isGroupInThisHost(group, targetMachine)) {
                System.out.println();
                System.out.println(group.getGroupName() + " 在machine " + targetMachine.getHost().getHostName());
                System.out.println("已经存在了");
                System.out.println("\n");
                groupFlag++;
                group = groupQueue.poll();
                groupQueue.offer(group);
                group = groupQueue.peek();
                if (groupFlag >= groupQueue.size()) {
                    //换机器
                    targetMachine = hostLowQueue.poll();
                    hostLowQueue.offer(targetMachine);
                    targetMachine = hostLowQueue.peek();
                    groupFlag = 0;
                }
            }
            //移完之后不超过阈值，并且这台机器上没有该Group
            if (targetMachine.getHost().getCount() + group.getCount() <= max) {
                System.out.println("将 " + group.getGroupName() + " 移动到： " + targetMachine.getHost().getHostName());
                int oldHostCount = targetMachine.getHost().getCount();
                targetMachine.getHost().setCount(oldHostCount + group.getCount());
                groupQueue.poll(); //这个Group移完了
                // System.out.println("移之前，count= " + oldHostCount + " ; 移之后，count= " + targetMachine.getHost().getCount());
                System.out.println("\n");
            } else {
                //移不了了
                HostGroups oldtTargetMachine = hostLowQueue.poll();
                targetMachine = hostLowQueue.peek();
                if (oldtTargetMachine.getHost().getCount() > targetMachine.getHost().getCount())
                    System.out.println("machine " + oldtTargetMachine.getHost().getHostName() + " 已经放不下了。所以将其弹出队列。");
                else {
                    System.out.println("machine " + targetMachine.getHost().getHostName() + " 放不了这个Group，所以换成下一台machine：" + targetMachine.getHost().getHostName());
                    hostLowQueue.offer(oldtTargetMachine);
                }
                System.out.println("\n");
            }
        }

        //待处理。放在 version 3
        while (!groupQueue.isEmpty()) {

        }

    }

    /**
     * 用于检测该group是否在hostGroups已经存在
     *
     * @param group         这个是待移动的Group
     * @param targetMachine 这个是目标机器
     * @return 返回一个布尔值，TRUE表示已经存在，FALSE表示不存在。
     */
    private static boolean isGroupInThisHost(Group group, HostGroups targetMachine) {
        for (Group groupTemp :
                targetMachine.getGroupList()) {
            if (group.getGroupName().equals(groupTemp.getGroupName())) {
                return true;
            }
        }
        return false;
    }

}
```





调用的工具类（核心是用的阿里提供的sampleJSON，对http请求返回的JSON数据进行解析。）

```java
/**
 * Created by peng.tan on 17/7/4.
 * 工具类
 */
public class GrepUtil {
    public HttpUtil httpUtil = new HttpUtil();

    /**
     * 根据hostname获取该host上面的所有Group
     *
     * @param hostName  主机名
     * @param startTime 开始时间
     * @param endTime   结束时间
     * @return
     */
    public List<Group> getGroupByHost(String hostName, String startTime, String endTime) {
        //排序器
        Comparator<Map.Entry<String, Integer>> valueComparator = Comparator.comparingInt(Map.Entry::getValue);
        String groupInHostURL = "xxxxxxx" + hostName + "%22%7D(0)%20by%20group%20range(" + startTime + "," + endTime + ")";
        String groupResult = httpUtil.getResult(groupInHostURL);
        TreeMap groupmap = new TreeMap<String, Integer>();
        JSON_group json_group = JSON.parseObject(groupResult, JSON_group.class);
        List<Group> list = new ArrayList<>();
        for (me.ele.sample.test.group.GroupedPoints item :
                json_group.getGroupedPoints()) {
            groupmap.put(item.getGroupBy().getGroup(), item.getPoints().getSummary().getCount());
        }
        List<Map.Entry<String, Integer>> grouplist = new ArrayList<Map.Entry<String, Integer>>(groupmap.entrySet());
        Collections.sort(grouplist, valueComparator);
        for (Map.Entry<String, Integer> entry :
                grouplist) {
            Group group = new Group();
            group.setGroupName(entry.getKey());
            group.setCount(entry.getValue());
            list.add(group);
        }
        return list;
    }

    /**
     * 用于获取所有的host信息
     *
     * @param hostName  这个是挂了的那台host的hostName
     * @param startTime 开始时间
     * @param endTime   结束时间
     * @return
     */
    public List<Host> gethosts(String hostName, String startTime, String endTime) {
        String result = "";
        HttpUtil httpUtil = new HttpUtil();
        String url = "xxxxxxxx(" + startTime + "," + endTime + ")";
        result = httpUtil.getResult(url);
        JSON_host j = JSON.parseObject(result, JSON_host.class);
        TreeMap<String, Integer> hostmap = new TreeMap<>();
        for (GroupedPoints item :
                j.getGroupedPoints()) {
            if (item.getGroupBy().getHostName().contains("xg-dal-c6") && !hostName.equals(item.getGroupBy().getHostName())) {
                hostmap.put(item.getGroupBy().getHostName(), Integer.parseInt(item.getPoint().getSummary().getCount() + ""));
            }
        }
        //j.getGroupedPoints().stream().filter(item -> item.getGroupBy().getHostName().contains("xg-dal-c6")).forEach(item -> hostmap.put(item.getGroupBy().getHostName(), Integer.parseInt(item.getPoint().getSummary().getCount() + "")));
        //按值排序, 升序，Java 8 新写法
        Comparator<Map.Entry<String, Integer>> valueComparator = Comparator.comparingInt(Map.Entry::getValue);
        //将Map转换成list进行排序
        List<Map.Entry<String, Integer>> hostlist = new ArrayList<Map.Entry<String, Integer>>(hostmap.entrySet());
        //使用Collection的排序
        Collections.sort(hostlist, valueComparator);
        List<Host> list = new ArrayList<>();
        for (Map.Entry<String, Integer> entry :
                hostlist) {
            Host host = new Host();
            host.setHostName(entry.getKey());
            host.setCount(entry.getValue());
            list.add(host);
        }
        return list;
    }

    /**
     * 根据hostname获取该host上面的所有Group。这里是查出挂了的那台host上面有哪些Group
     *
     * @param hostName  主机名
     * @param startTime 开始时间
     * @param endTime   结束时间
     * @return 返回的是一个队列
     */
    public LinkedList<Group> getGroupQueueByHost(String hostName, String startTime, String endTime) {
        //排序器
        Comparator<Map.Entry<String, Integer>> valueComparator = Comparator.comparingInt(Map.Entry::getValue);
        String groupInHostURL = "xxxxxx" + hostName + "%22%7D(0)%20by%20group%20range(" + startTime + "," + endTime + ")";
        String groupResult = httpUtil.getResult(groupInHostURL);
        TreeMap groupmap = new TreeMap<String, Integer>();
        JSON_group json_group = JSON.parseObject(groupResult, JSON_group.class);
        LinkedList<Group> list = new LinkedList<Group>();
        for (me.ele.sample.test.group.GroupedPoints item :
                json_group.getGroupedPoints()) {
            groupmap.put(item.getGroupBy().getGroup(), item.getPoints().getSummary().getCount());
        }
        List<Map.Entry<String, Integer>> grouplist = new ArrayList<Map.Entry<String, Integer>>(groupmap.entrySet());
        Collections.sort(grouplist, valueComparator);
        for (Map.Entry<String, Integer> entry :
                grouplist) {
            Group group = new Group();
            group.setGroupName(entry.getKey());
            group.setCount(entry.getValue());
            list.offer(group);
        }
        return list;
    }
}

```



Http请求工具类：

```java
/**
 * Created by peng.tan on 17/6/30.
 */
public class HttpUtil {
    public String getResult(String url){
        CloseableHttpClient httpclient = HttpClients.createDefault();
        String result = "";
        try {
            // 创建httpget.
            HttpGet httpget = new HttpGet(url);
//            System.out.println("executing request " + httpget.getURI());
            // 执行get请求.
            CloseableHttpResponse response = httpclient.execute(httpget);
            try {
                // 获取响应实体
                HttpEntity entity = response.getEntity();
                // 打印响应状态
//                System.out.println(response.getStatusLine());
                if (entity != null) {
                    // 打印响应内容长度
//                    System.out.println("Response content length: " + entity.getContentLength());
                    // 打印响应内容
                    result = EntityUtils.toString(entity);
//                    System.out.println("Response content: " + result);
                }
//                System.out.println("------------------------------------");
                return result;
            } finally {
                response.close();
            }
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (ParseException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 关闭连接,释放资源
            try {
                httpclient.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return "";
    }
}
```



运行效果图：

![屏幕快照 2017-07-06 下午5.41.41](https://ws1.sinaimg.cn/large/006tKfTcgy1fhab0ho63fj30g40ilwgf.jpg)

