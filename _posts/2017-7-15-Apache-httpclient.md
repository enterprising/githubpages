---
layout: post
title: Apache httpclient
date: 2017-7-15 14:00
categories: Java
tags: [Java]
---

* content
{:toc}
用了这么多年的Apache httpclient，但一直没有对它进行过详细的了解，只会单纯地使用。现在趁周末对它进行稍微详细的介绍。

# 1、引用的jar包

maven项目里面只需要在pom.xml里面加一句话：

```java
<!-- https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient -->
<dependency>
	<groupId>org.apache.httpcomponents</groupId>
	<artifactId>httpclient</artifactId>
	<version>4.5.2</version>
</dependency>
```

这里用的是4.5.2版，属于非常稳定的一个版本了。

如果不是maven项目，去Apache的官网下载jar包然后导入项目里面，效果也是一样的。

整个httpclient需要用到的包，详细的看有：

```java
import org.apache.http.HttpEntity;
import org.apache.http.ParseException;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;
```

# 2、过程详解

## 2.1创建一个HpptClient对象

> CloseableHttpClient httpclient = HttpClients.createDefault();

这里创建的是一个可以关闭的client

## 2.2创建一个HttpGet对象

> HttpGet httpget = new HttpGet(url);

## 2.3执行get请求，返回httpresponse

> CloseableHttpResponse response = httpclient.execute(httpget);

## 2.4获取响应实体 HttpEntity

> HttpEntity entity = response.getEntity();

## 2.5利用EntityUtils类的toString方法，将响应实体转成字符串

> String result = EntityUtils.toString(entity);

## 2.6关闭response、关闭httpclient

注意关闭响应结果、关闭连接，避免造成资源的浪费

# 3、代码

```java
/**
 * Created by peng.tan on 17/6/30.
 */
public class HttpUtil {
    public String getResult(String url) {
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
        return result;
    }
}
```

