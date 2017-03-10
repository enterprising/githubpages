---
layout: post
title: EL表达式中的if.....else....使用方法
date: 2017-3-10 16:20
categories: JavaWeb
tags: [JavaWeb]
---
# EL表达式的 if else 用法
之前在使用EL里面的if的时候，总是找不到else或者说用了else之后无效。今天看了一篇博客之后发现其实EL表达式里面是没有if else这种分支结构的。但别担心，还是有类似的东西存在的。

## choose-  when  otherwise

```java
	
	 <c:choose>  
	      <c:when test="${!empty user.userName}">  
	          欢迎您，${user.userName}  
	      </c:when>  
	      <c:otherwise>  
	          <a href="login.jsp">登录</a>  <a href="regedit.jsp">注册</a>  
	      </c:otherwise>  
	 </c:choose>  

```