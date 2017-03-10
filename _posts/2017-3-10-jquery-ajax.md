---
layout: post
title: jquery的ajax传值以及接收action返回的值
date: 2017-3-10 16:25
categories: JavaWeb
tags: [ajax,jQuery,JavaWeb]
---
# jquery的ajax传值以及接收action返回的值
这里涉及到一个很重要的东西： action页面传回的最好设置成json格式的内容。
# 参考代码
## servlet
```java
response.setContentType("text/json;charset=utf-8");
response.getWriter().println(
      "{\"name\":\""+re+"\"}");
System.out.println("{name:\""
 + request.getParameter("cname") + "\"}");

```
## js中接收
```js
$(document).ready(function() {
//使用jQuery异步提交表单
$("#submitForm").submit(function() {
       $.ajax({
            url : '${pageContext.request.contextPath}/class.do?method=checkClass',
            data : $('#submitForm').serialize(),
            type : "POST",
            dataType: "json",
            beforeSend : function() {
                 //在异步提交前要做的操作
            },
            success : function(data) {
                 if(data.name=="1"){
                      $("#mes").css("visibility","visible");
                      $("#mes").text("该班级已存在！");
                      //$("#cname").attr("value",'');
                 }else{
                      $("#mes").css("visibility","hidden");
                      layer.alert('新增班级成功', {
                        skin: 'layui-layer-molv' //样式类名
                        ,closeBtn: 0
                      }, function(){
                         location = "class.do?method=addClass&cname="+$("#cname").val()+"&x="+Math.random();
                      });
                 }
            },
            error: function (data) {
          }
       });
       return false;
  });
});

```