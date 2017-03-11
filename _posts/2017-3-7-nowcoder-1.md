---
layout: post
title: 超级素数幂
date: 2017-3-7 21:20
categories: 算法
tags: [算法]
---
# 超级素数幂
## 题目描述
> 如果一个数字能表示为p^q(^表示幂运算)且p为一个素数,q为大于1的正整数就称这个数叫做超级素数幂。现在给出一个正整数n,如果n是一个超级素数幂需要找出对应的p,q。

## 最开始的思路 （40%通过率）
> 说明: 我最开始用的是暴力破解的方法，先找出n开方下面的所有的素数存到一个list下面，最后暴力的一个个的试。通过了40%。

## 代码：
``` java
public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
	while (sc.hasNext()) {
		long n = sc.nextLong();
		check(n);
	}
	sc.close();
}

private static void check(long n) {
	List<Integer> sushu = new ArrayList<Integer>();
	for (int i = 2; i < Math.sqrt(n); i++) {
		// 判断素数
		int flag = 0;
		for (int j = 2; j <= Math.sqrt(i); j++) {
			if (i % j == 0) {
				flag = 1;
				break;
			}
		}
		if (flag == 0)
			sushu.add(i);
	}
	int p = 0;
	int q = 1;
	int flag = 0;
	for (int i = 0; i < sushu.size(); i++) {
		p = (int) sushu.get(i);
		q = 1;
		while (n % p == 0 && n != p) {
			q++;
			n = n / p;
		}
		if (n == p) {
			flag = 1;
			break;
		}else{
			flag = 0;
		}
	}
	if (flag == 1) {
		System.out.println(p + " " + q);
	} else {
		System.out.println("No");
	}
}
```
## 正确的思路
> 其实上面的思路基本上正确，只是有一个地方卡住了，那就是我是先找素数p，最后再一个个试出q，这是非常愚蠢的方法，铁定超时。<br>
> 仔细想想，先确定q，这样不是更好吗？

## 代码
```java
public static void main(String[] args) {
		Scanner in = new Scanner(System.in);
		long n = in.nextLong();
		boolean f = false;
		if (n >= 2 && n <= Math.pow(10, 18)) {
			double p;
			int q = 2;
			p = (int) (Math.pow(n, 1.0 / q));
			while (p >= 2) {
				p = (int) (Math.pow(n, 1.0 / q));
				if (Math.pow(p, q) == n) {
					f = check(p, q);
				}
				q++;
			}
			if (f == false)
				System.out.println("No");
		} else {
			System.out.println("输入错误！");
		}
		in.close();
	}

	private static boolean check(double p, int q) {
		int flag = 0;
		for (int j = 2; j <= Math.sqrt(p); j++) {
			if (p % j == 0) {
				flag = 1;
				break;
			}
		}
		if (flag == 0) {
			System.out.println((int)p + " " + q);
			return true;
		} else {
			return false;
		}
	}
```