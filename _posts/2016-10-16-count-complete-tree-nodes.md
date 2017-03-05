---
layout: post
title: 222.Count Complete Tree Nodes(Medium)
date: 2016-10-16 11:58
categories: LeetCode
tags: [LeetCode,Medium,二叉树,计数,递归]
---

>Given a **complete** binary tree, count the number of nodes.
给定一个完全二叉树，计算其节点数

>**Definition of a complete binary tree from [Wikipedia](http://en.wikipedia.org/wiki/Binary_tree#Types_of_binary_trees):**In a complete binary tree every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible. It can have between 1 and 2h
 nodes inclusive at the last level h.

*　　难度在于其如何优化递归，缩短递归的次数*

# My Solution
### (Java) Version 1  Time: *Time Limit Exceeded*:
　　遍历一个树，最先想到的方法果然是递归，随手就可以写出一个最简单的递归遍历，当然，不出意外的话，这是一定会超时的，果然就超时了，只是写法很容易理解，留在这里作为纪念，其次是想说，无脑没有优化的递归确实是相当慢
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public int countNodes(TreeNode root) {
        if(root == null){
            return 0;
        }
        else{
            return 1+countNodes(root.left)+countNodes(root.right);
        }
    }
}
```
### (Java) Version 2  Time: 111ms:
　　这里真的是日了dog了，我只能说向位运算低头，原来用Math.pow()来计算满二叉树的几点，然后就超时了，换成<<之后就赤果果地过了，我的天……
　　好了，说一下思路，直接遍历每一个节点是不现实的，所以必须通过完全二叉树的特点来计算，我们可以想到，除了最下的那一层，其余的部分，都是满二叉树，这样我们首先可以判断当前的二叉树是不是满二叉树，判断的方法是判断树最左和最右两边的长度是否相等，如果相等就可以直接计算，如果不等就进入递归，分别计算左右两颗子树，知道只有一个节点的时候就停止。
　　因为完全二叉树进行左右分割之后，很容易就会出现满二叉树，所以节省了大量的遍历节点的时间
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public int countNodes(TreeNode root) {
        int count_left = count_length_l(root);
        int count_right = count_length_r(root);
        if(root == null){
            return 0;
        }
        else{
            if(count_left == count_right){
                return (1 << count_left)-1;
            }
            else{
                return countNodes(root.left)+countNodes(root.right)+1;
            }
        }
    }

    public int count_length_l(TreeNode root){
        int count = 0;
        while(root!=null){
            count++;
            root = root.left;
        }
        return count;
    }

    public int count_length_r(TreeNode root){
        int count = 0;
        while(root!=null){
            count++;
            root = root.right;
        }
        return count;
    }
}
```
### (Java) Version 3  Time: 79ms:
　　事实上上面那个方法就已经过了的，不过当时没有想到位运算，所以以为是遍历的时间太长，于是重新写了下面的这个方法，时间果然快很多，当然如果没有位运算还是会超时。
　　思路是把二叉树分为左边一颗子树和右边一颗子树，分别计算两棵树的最左边，然后比较，如果相等的话，就表明左边的子树是满二叉树（是不是很巧妙），如果不等的话，就表明右边的子树是满二叉树（想一下满二叉树的结构），然后分别进入递归就行了，我没有认真计算时间复杂度，但是从巧妙程度来看应该是比上面的方法快的
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public int countNodes(TreeNode root) {
        if(root == null){
            return 0;
        }
        int count_left = count_length(root.left);
        int count_right = count_length(root.right);
        if(count_left == count_right){
            return (1 << count_left)+countNodes(root.right);
        }
        else{
            return (1 << count_right)+countNodes(root.left);
        }
    }
    
    public int count_length(TreeNode root){
        int count = 0;
        while(root!=null){
            count++;
            root = root.left;
        }
        return count;
    }
}
```
　　很高兴的一点是，我自己写的两种遍历方式似乎就已经是discuss中的两大主流方法了，基本上他们的方法也是这两种实现，只是写法有一点不同……所以下面贴了一个最详细的大神的多种解法，好像挺有意思的……
### (Java) Version 4  Time: 102ms (By [StefanPochmann](https://discuss.leetcode.com/user/stefanpochmann)):
　　简短一直都是推崇的原因之一
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    int height(TreeNode root) {
        return root == null ? -1 : 1 + height(root.left);
    }
    public int countNodes(TreeNode root) {
        int h = height(root);
        return h < 0 ? 0 :
               height(root.right) == h-1 ? (1 << h) + countNodes(root.right)
                                         : (1 << h-1) + countNodes(root.left);
    }
}
```
### (Java) Version 5  Time: 63ms (By [StefanPochmann](https://discuss.leetcode.com/user/stefanpochmann)):
　　果然一旦摆脱了递归之后速度就扶摇直上，这个是一个迭代的实现方式，确实需要好好学习一下如何把递归转化为迭代实现……
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    int height(TreeNode root) {
        return root == null ? -1 : 1 + height(root.left);
    }
    public int countNodes(TreeNode root) {
        int nodes = 0, h = height(root);
        while (root != null) {
            if (height(root.right) == h - 1) {
                nodes += 1 << h;
                root = root.right;
            } else {
                nodes += 1 << h-1;
                root = root.left;
            }
            h--;
        }
        return nodes;
    }
}
```
### (Java) Version 6  Time: 75ms (By [StefanPochmann](https://discuss.leetcode.com/user/stefanpochmann)):
　　中间循环的实现原理还没有细看，应该是把高度方法放进了一个方法里面，其中的整合还有待琢磨，应该有其中的巧妙之处
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int countNodes(TreeNode root) {
        if (root == null)
            return 0;
        TreeNode left = root, right = root;
        int height = 0;
        while (right != null) {
            left = left.left;
            right = right.right;
            height++;
        }
        if (left == null)
            return (1 << height) - 1;
        return 1 + countNodes(root.left) + countNodes(root.right);
    }
}
```