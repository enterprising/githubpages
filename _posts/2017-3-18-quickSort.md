---
layout: post
title: 快速排序
date: 2017-03-18 08:40
categories: 排序
tags: [排序,笔试面试]
---

## 快速排序的关键：在数组中选择一个数字，比它小的放左边，比它大的放右边

## 切分算法
```java
private static int patition(int[] array, int low, int hight) {
		int key = array[low]; // 数组的第一个作为中轴
		while (low < hight) {
			// 从后半段向前半段扫描
			while (array[hight] >= key && hight > low) {
				hight--;
			}
			array[low] = array[hight]; // 比中轴小的移到低端
			// 从前半段向后半段扫描
			while (array[low] <= key && hight > low) {
				low++;
			}
			array[hight] = array[low]; // 比中轴大的移到高端
		}
		array[low] = key;
		return low;
	}
```
## 快排的递归
```java
public static void quicksort(int[] array, int low, int hight) {
		if (array == null || low > hight)
			return;
		int index = patition(array, low, hight);
		quicksort(array, low, index - 1);  //对前一部分进行排序
		quicksort(array, index + 1, hight);	//对后一部分进行排序
	}
```
## 主方法调用
```java
public static void main(String[] args) {
		int[] a = { 6, 3, 7, 4, 1 };
		quicksort(a, 0, a.length - 1);
		for (int i : a) {
			System.out.print(i + "\t");
		}
	}
```
## 运行结果
![enter description here][1]

## 优化方法
在待排序的数据较小的时候，比如10，进行直接插入排序
```java
public static void quick(int []array ,int lo,int hi){
        if(hi-lo+1<10){
            insertSort(array);
        }else{
            quickSort(array,lo,hi);
        }
    }
```


  [1]: http://omphwvjh0.bkt.clouddn.com/1489814954995.jpg "快速排序"