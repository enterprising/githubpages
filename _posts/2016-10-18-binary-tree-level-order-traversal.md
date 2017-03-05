---
layout: post
title: 102.Binary Tree Level Order Traversal(Easy)
date: 2016-10-18 14:46
categories: LeetCode
tags: [LeetCode,Easy,二叉树,队列]
---

>Given a binary tree, return the *level order* traversal of its nodes' values. (ie, from left to right, level by level).
给定一个二叉树，返回他的水平层序遍历（从左到右，一层再一层）

*　　一个对队列的巧妙应用*

**For example**
>Given binary tree [3,9,20,null,null,15,7].
**  3
 /   \
9   20
     /   \
   15   7**
return its level order traversal as:
**[ [3], [9,20], [15,7] ]**

# My Solution
### (Java) Version 1  Time: 3ms:
　　队列这个结构就巧妙地把二叉树的立体结构变成线性结构了，也是一种巧妙地遍历方式，先遍历每个小树的根结点，然后把所有子节点都放进队列中，然后对队列进行遍历，用size这个变量来保证每次都刚好遍历了一层，知道队列中没有元素了，就表示树没有再发现新的子节点，于是结束遍历
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
    public List<List<Integer>> levelOrder(TreeNode root) {
        if(root == null){
            return new ArrayList<List<Integer>>();
        }
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        List<Integer> first = new ArrayList<Integer>();
        first.add(root.val);
        result.add(first);
        ArrayDeque<TreeNode> ad = new ArrayDeque<TreeNode>();
        ad.add(root);
        while(ad.size()!=0){
            int size = ad.size();
            List<Integer> temp = new ArrayList<Integer>();
            for(int i = 0;i < size;i++){
            TreeNode node = ad.removeFirst();
            if(node.left!=null){
                ad.add(node.left);
                temp.add(node.left.val);
            }
            if(node.right!=null){
                ad.add(node.right);
                temp.add(node.right.val);
            }
            }
            if(!temp.isEmpty())
                result.add(temp);
        }
        return result;
    }
}
```