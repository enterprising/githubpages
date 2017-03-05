---
layout: post
title: 206.Reverse Linked List(Easy)
date: 2016-10-07 13:23
categories: LeetCode
tags: [LeetCode,Easy,链表]
---

>Reverse a singly linked list.
倒序输出一个单链表

# My Solution
### (Java) Version 1  Time: 0ms:
　　这个本来应该是没有难度的，不过倒是Java自己给我挖了一个坑，Java使用的引用其实就是指针，如果类没有clone方法的话，那么就不能像C语言一样直接用一个变量把某个引用的值保存下来，就只能用一个新的值分别储存其中要使用的值，这确实是一个大坑，Java号称没有指针，其实就是强制全部都用指针的意思吧
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }else if(head.next == null){
            return head;
        }
        ListNode temp = head, next = head.next, newhead;//必须要用一个next变量把下一个结点的引用保存下来
        temp.next = null;
        newhead = temp;
        head = next;
        do {
            temp = head;
            next = head.next;
            temp.next = newhead;
            newhead = temp;
            head = next;
        } while (head != null);
        return newhead;
    }
}
```
### (Java) Version 2  Time: 0ms (By [arjun033](https://discuss.leetcode.com/user/arjun033) ):
　　其实也是一样的方式，不过思路更加清晰，用的方式也比较简单，方便阅读理解
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode reverseList(ListNode head) {
        if(head==null||head.next==null) return head;
        ListNode prev, curr, next;
        prev = null;
        curr = head;
        next = null;
        
        while(curr!=null){
            next=curr.next;
            curr.next=prev;
            prev=curr;
            curr=next;
        }
        return prev;
    }
}
```