---
title: "验证二叉搜索树"
date: 2021-09-04T14:56:12+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。

有效 二叉搜索树定义如下：

* 节点的左子树只包含 小于 当前节点的数。
* 节点的右子树只包含 大于 当前节点的数。
* 所有左子树和右子树自身必须也是二叉搜索树。

# 解法
根据定义，递归地限制子树所有结点的取值上下界，来保证复合定义即可

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
    public boolean isValidBST(TreeNode root) {
        return sub(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private boolean sub(TreeNode root, long l, long r) {
        if (root == null) {
            return true;
        }
        if (root.val <= l || root.val >= r) {
            return false;
        }
        return sub(root.left, l, root.val) && sub(root.right, root.val, r);
    }
}
```