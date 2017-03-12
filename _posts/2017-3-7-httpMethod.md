---
layout: post
title: HTTP的八种方法（“动作”）
date: 2017-3-7 21:50
categories: 计算机网络
tags: [计算机网络]
---
# 这八种方法用于请求URL资源的不同的操作方式
## 1、options
询问可以执行哪些方法
## 2、head
类似于GET，但是不返回body信息，用于检查对象是否存在以及得到对象的元数据
## 3、GET
向特定的资源发出请求。注意：GET方法不应当被用于产生“副作用”的操作中，例如在Web Application中，其中一个原因是GET可能会被网络蜘蛛等随意访问。Loadrunner中对应get请求函数：web_link和web_url
## 4、POST
向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。 Loadrunner中对应POST请求函数：web_submit_data,web_submit_form
## 5、put
想指定资源位置上传其最新内容
## 6、delete
请求服务器删除Request-URL所标识的资源
## 7、trace
用于远程诊断服务器

## 关于各种方法的安全性
> HEAD，GET，OPTIONS、TRACE视为安全的方法，因为他们只是从服务器获取资源而不对服务器做任何修改，
但是HEAD、GET、OPTIONS在用户端不安全。而POST则影响服务器上的资源
综上可得 TRACE才是相对于服务器和客户端都安全的方法。
