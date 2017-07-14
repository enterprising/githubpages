---
layout: post
title: JSP页面用EL表达式 输出date格式
date: 2017-3-10 16:25
categories: JavaWeb
tags: [JavaWeb]
---
* content
{:toc}
# JSP页面用EL表达式 输出date格式

## 1、顶上引入标签
```java
	<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
	<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>
```

## 2、fmt函数的使用
```java
	<fmt:formatDate value="${project.creatDate }" pattern="yyyy-MM-dd"/>
```