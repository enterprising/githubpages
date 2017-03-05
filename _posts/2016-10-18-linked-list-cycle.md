---
layout: post
title: 141.Linked List Cycle(Easy)
date: 2016-10-18 15:15
categories: LeetCode
tags: [LeetCode,Easy,链表]
---

>Given a linked list, determine if it has a cycle in it.
给定一个线性链表，判断它是否有环

*　　题目很久之前做过，那时候一个方法都不懂，成环的意思，就是链表的尾结点连接到了链表中的某个节点，形成了一个环形的结构*

# My Solution
### (Java) Version 1  Time: *Time Limit Exceeded*:
　　这是以前什么都不懂时候的做法，当然这个效果肯定是有的，不过效率一般般，之前是C语言写的，现在用Java复现一下，留作纪念。这里用的方式是，在遍历链表的时候把遍历过的结点保存下来，然后在遍历下一个结点的时候，确定结点中的next指向是否在遍历过的结点中。
```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null)return false;
        List<ListNode> list = new ArrayList<ListNode>();
        list.add(head);
        ListNode current = head.next;
        while(current!=null){
            list.add(current);
            if(list.indexOf(current.next)!=-1)return true;
            current = current.next;
        }
        return false;
    }
}
```
### (Java) Version 2  Time: 1ms:
　　这当然不是我自己的方法，是以前学来的，如果我能想到这么巧妙的方法就是大神了。
　　思路其实不难理解，设置两个浮标，一个在前一个在后，前面的浮标每次都比后面的浮标走多一步，这样越走越快，然后前面的浮标一定会先进入环中（如果有的话），然后再从后面追上走得慢的浮标，如果发现慢的浮标被快的浮标追上了，那么说明有环
　　我一直在疑惑如果每次都刚好错过了会不会有这个可能，不过看起来这个方法倒是确实效率很高，这应该是数学上证明过的。
```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null)return false;
        ListNode fast = head,slow = head;
        while(fast!=null&&fast.next!=null){
            slow = slow.next;
            fast = fast.next.next;
            if(fast == slow)break;
        }
        if(fast == null||fast.next == null){
            return false;
        }else{
            return true;
        }
    }
}
```