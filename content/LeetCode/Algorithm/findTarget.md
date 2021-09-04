---
title: "两数之和-BST"
date: 2021-09-04T15:09:41+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个二叉搜索树 root 和一个目标结果 k，如果 BST 中存在两个元素且它们的和等于给定的目标结果，则返回 true。

# 解法
深度优先遍历结合哈希表，像数组那样解
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
    Map<Integer, Integer> dic = new HashMap<>();
    public boolean findTarget(TreeNode root, int k) {
        if (root != null) {
            if (dic.containsKey(k-root.val)) {
                return true;
            }
            dic.put(root.val, 1);
            return findTarget(root.left, k) || findTarget(root.right, k);
        }
        return false;
    }
}
```