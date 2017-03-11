---
layout: post
title: 序列和
date: 2017-3-7 21:20
categories: 算法
tags: [算法]
---
# 序列和 （基本上算是数学问题了）
## 题目描述
> 给出一个正整数N和长度L，找出一段长度大于等于L的连续非负整数，他们的和恰好为N。答案可能有> > 多个，我我们需要找出长度最小的那个。<br>
> 例如 N = 18 L = 2：<br>
> 5 + 6 + 7 = 18 <br>
> 3 + 4 + 5 + 6 = 18<br>
> 都是满足要求的，但是我们输出更短的 5 6 7<br>

## 思路
> 这里最重要的问题就是确定起始位置start和长度L，然后用递增数列的公式就能算出总和。<br>
> 里面有个很重要的陷阱，要先找L，再慢慢搞start，不然时间复杂度太高。

## 参考代码
```java
public static void main(String[] args) {
	Scanner in = new Scanner(System.in);
	int n = in.nextInt();
	int l = in.nextInt();
	if (n >= 1 && n <= 1000000000)
		check(n, l);
	else
		System.out.println("输入有误");
	in.close();
}

private static void check(int n, int l_) {
	boolean f = false;
	int star = -1;
	int l = -1;
	for (l = l_ ; l <= 100; l++) {
		if ((n - l * (l - 1) / 2) >= 0 && (n - l * (l - 1) / 2) % l == 0) {
			star = (n - l * (l - 1) / 2) / l;
			f = true;
			int j = 0;
			for (j = star; j < star + l - 1; j++) {
				System.out.print(j + " ");
			}
			System.out.println(j);
			return;
		}
	}
	if (f != true) {
		System.out.println("No");
	}
}

private static void check2(int n, int l_) {
	boolean f = false;
	int star = -1;
	int l = -1;
	for (l = l_ + 1; l <= 100; l++) {
		for (int i = 1; i < n / 2; i++) {
			int sum = l * i + l * (l - 1) / 2;
			// System.out.println("sum=" + sum);
			if (sum == n) {
				star = i;
				f = true;
				break;
			}
		}
		if (f == true)
			break;
	}
	if (f == true) {
		int j = 0;
		for (j = star; j < star + l - 1; j++) {
			System.out.print(j + " ");
		}
		System.out.println(j);
	} else {
		System.out.println("No");
	}
}
```