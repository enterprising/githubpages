---
layout: post
title: 328.Odd Even Linked List(Medium)
date: 2016-11-01 23:22
categories: LeetCode
tags: [LeetCode,Medium,链表]
---

>Given a singly linked list, group all odd nodes together followed by the even nodes. 
Please note here we are talking about the node number and not the value in the nodes.
You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

*　　题目不难理解我就不翻译了，就是一个单链表，把所有奇数的结点放在前面，然后接着所有的偶数结点，然后要求在时间复杂度O(n)和空间复杂度O(1)之下完成*

**Example:**
>Given 1->2->3->4->5->NULL,
return 1->3->5->2->4->NULL.

**Note:**
>The relative order inside both the even and odd groups should remain as it was in the input. The first node is considered odd, the second node even and so on ...

# My Solution
### (Java) Version 1  Time: 0ms:
　　一开始我以为和很久之前的题一样，Java在单链表会有坑，然后尝试了多用几个变量就会超过空间限制，后来破罐子破摔用回C语言的写法没有想到过了。
　　思路在于，把单链表分为奇数结点的链表和偶数结点链表两个，然后遍历链表分别把奇数结点和偶数结点提取出来，最后在两条链表一接，完成……
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
    public ListNode oddEvenList(ListNode head) {
        if( head == null || head.next == null || head.next.next == null)
        return head ;
        ListNode odd = head , even = head.next , even_head = even;
        while(even != null &&  even.next != null ){
            odd.next = even.next ;
            even.next = odd.next.next ;
            odd = odd.next ;
            even = even.next;
        }
        odd.next = even_head;
        return head;
    }
}
```
　　这破题没有别的做法……