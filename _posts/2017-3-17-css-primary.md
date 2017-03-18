---
layout: post
title: css选择器的优先级
date: 2017-03-17 19:20
categories: css
tags: [css]
---
> 选择器的优先级 <br>
> 1.最高优先级是 (直接在标签中的设置样式，假设级别为1000) <br>
> 2.次优先级是（ID选择器 ,假设级别为100）   #myDiv{color:Red;} <br>
> 3.其次优先级是（类选择器，假设级别为10） .divClass{color:Red;}<br>
> 4.最后优先级是 （标签选择器，假设级别是 1）  div{color:Red;}<br>
> 5.那么后代选择器的优先级就可以计算了啊<br>
　  比如 .divClass  span { color:Red;}   优先级别就是：10+1=11
  
上面的第一点：
```css
<div style="color:Red;"></div>
```