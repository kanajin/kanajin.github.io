---
title: "二叉搜索树中的插入操作"
date: 2021-09-03T12:29:54+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据 保证 ，新值和原始二叉搜索树中的任意节点值都不同。

注意，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。 你可以返回 任意有效的结果 。

# 解法
## 递归法
如果比当前根值大，递归左子树，如果左子树为空，插入  
反之

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
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val, null, null);
        }
        if (val < root.val) {
            root.left = insertIntoBST(root.left, val);
        }
        if (val > root.val) {
            root.right = insertIntoBST(root.right, val);
        }
        return root;
    }
}
```

## 模拟法
创建一个探索结点pos，和val进行大小比较，如果 val < pos.val，pos就向左子树移动，如果是null，就将val插入

```java
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val, null, null);
        }
        TreeNode pos = root;
        while (pos != null) {
            if (val < pos.val) {
                if (pos.left == null) {
                    pos.left = new TreeNode(val, null, null);
                    break;
                }
                else {
                    pos = pos.left;
                }
            }
            else {
                if (pos.right == null) {
                    pos.right = new TreeNode(val, null, null);
                    break;
                }
                else {
                    pos = pos.right;
                }
            }
        }
        return root;
    }
}
```