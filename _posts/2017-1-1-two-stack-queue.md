---
layout:post
title:用两个栈实现一个队列
date:2017-1-2 8:00
categories:剑指offer
tags:[剑指offer]
---
# 题目描述
> 用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

# 思路
> 栈的性质是先进后出 <br>
> 队列的性质是先进先出<br>
> 有两个栈，s1和s2，然后我们要实现push和pop方法 <br>
> **push:**<br>
> 直接在s1里面加入就好<br>
> **pop:** <br>
> s2为空，然后我们弹出是要弹出s1栈底的那个元素，这时候就需要用到s2了，先把s1所有的元素都push进s2，然后s2.pop一下，弹出最上面那个元素，这样就完美的解决了

# 代码
```java
import java.util.Stack;
public class Solution{
	Stack<Integer> s1 = new Stack<Integer>();
	Stack<Integer> s2 = new Stack<Integer>();
	public void push(int code){
		s1.push(code);
	}
	public int pop(){
		if(s2.empty()&&s1.empty()){
			//输出 stack is empty
		}
		if(s2.empty()){
			//将s1的内容push进s2
			while(!s1.empty()){
				s2.push(s1.pop());
			}
		}
		return s2.pop();
	}
}
```