---
title: "二叉树的最大深度"
date: 2021-09-01T21:23:56+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

# 解法
与上一题的广度优先遍历相对，此为深度优先遍历。  

## 回溯
我们知道，先序、中序、后续都为深度优先，因此可以借用此思路，用回溯算法  
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    int max_count = 0;

    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        inOrder(root, 0);
        return max_count;
    }

    private void inOrder(TreeNode root, int count) {
        if (root != null) {
            inOrder(root.left, count+1);
            inOrder(root.right, count+1);
        }
        if (max_count < count) {
            max_count = count;
        }
    }
}
```

## 深度优先递归
求一个树的最大深度，可以先求其左右子树的深度，再取最大值并+1。而对于其左右子树也是如此，形成递归

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left_depth = maxDepth(root.left);
        int right_depth = maxDepth(root.right);
        return Math.max(left_depth, right_depth)+1;
    }
}
```