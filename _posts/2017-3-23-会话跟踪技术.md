---
layout: post
title: Web的会话跟踪技术
date: 2017-03-23 08:40
categories: JavaWeb
tags: [JavaWeb,笔试面试]
---
![enter description here][1]

ABCD   
会话跟踪是一种灵活、轻便的机制，它使Web上的状态编程变为可能。  
HTTP是一种无状态协议，每当用户发出请求时，服务器就会做出响应，客户端与服务器之间的联系是离散的、非连续的。当用户在同一网站的多个页面之间转换时，根本无法确定是否是同一个客户，会话跟踪技术就可以解决这个问题。当一个客户在多个页面间切换时，服务器会保存该用户的信息。  
有四种方法可以实现会话跟踪技术：URL重写、隐藏表单域、Cookie、Session。  
1）.隐藏表单域：```<input type="hidden">```，非常适合步需要大量数据存储的会话应用。  
2）.URL 重写:URL 可以在后面附加参数，和服务器的请求一起发送，这些参数为名字/值对。  
3）.Cookie:一个 Cookie 是一个小的，已命名数据元素。服务器使用 SET-Cookie 头标将它作为 HTTP  
响应的一部分传送到客户端，客户端被请求保存 Cookie 值，在对同一服务器的后续请求使用一个  
Cookie 头标将之返回到服务器。与其它技术比较，Cookie 的一个优点是在浏览器会话结束后，甚至在客户端计算机重启后它仍可以保留其值  
4）.Session：使用 setAttribute(String str,Object obj)方法将对象捆绑到一个会话  



  [1]: http://omphwvjh0.bkt.clouddn.com/1490232287488.jpg "会话跟踪技术"