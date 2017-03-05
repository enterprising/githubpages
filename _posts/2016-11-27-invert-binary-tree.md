---
layout: post
title: 226.Invert Binary Tree(Easy)
date: 2016-11-27 14:32
categories: LeetCode
tags: [LeetCode,Easy,二叉树,递归]
---

>Invert a binary tree.
反转二叉树

*　　基本上二叉树的玩意儿用递归都能做*

**For example**

>
![](http://olwt21mf4.bkt.clouddn.com/17-2-26/5478340-file_1488083295712_4073.png)
to
![](http://olwt21mf4.bkt.clouddn.com/17-2-26/21614431-file_1488083295582_9f15.png)

# My Solution
### (Java) Version 1  Time: 1ms:
　　简单地递归然后调换左右子树
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        root.left = invertTree(root.left);
        root.right = invertTree(root.right);

        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        return root;
    }
}
```
### (Java) Version 2  Time: 0ms (By [anjani_sureshbhat](https://discuss.leetcode.com/user/anjani_sureshbhat)):
　　有递归调用自然就有非递归调用，这就是一个迭代的版本，然而我的迭代写得并不好，还要多看，当然还有一个很重要的就是迭代比递归快
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }
        Queue<TreeNode> q = new LinkedList<TreeNode>();
        q.offer(root);
        while (!q.isEmpty()) {
            TreeNode polled = q.poll();
            if (polled.left != null || polled.right != null) {
                TreeNode temp = polled.left;
                polled.left = polled.right;
                polled.right = temp;
                if (polled.left != null) q.offer(polled.left);
                if (polled.right != null) q.offer(polled.right);
            }
        }
        return root;
    }
}
```