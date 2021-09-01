---
title: "二叉树的层序遍历"
date: 2021-09-01T20:55:59+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。

# 解法
层序遍历，即广度优先遍历。  

与之相对的，先序、中序、后续遍历称为深度优先遍历，即先一条路走到头，再回来遍历其他的。  

广度优先遍历使用队列来解决：同一层的结点进入队列，然后取出的同时放入他的子结点。由于队列的先进先出特性，父结点会比子结点先遍历完毕。  

既然是同时取出并插入，那就需要判断父结点的数目，以确定需要取出几次：在进行下一次迭代前，先记录下当前队列的长度，该长度值即为父结点数量，迭代相应次数即可。

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> level_List = new ArrayList<>();
        if (root == null) {
            return level_List;
        }
        //这行为java生成队列的代码
        Queue<TreeNode> que = new LinkedList<>();
        que.offer(root);
        while (que.size() != 0) {
            List<Integer> temp = new ArrayList<>();
            int level = que.size();
            for (int i = 0; i < level; i++) {
                TreeNode node = que.poll();
                temp.add(node.val);
                if (node.left != null) {
                    que.offer(node.left);
                }
                if (node.right != null) {
                    que.offer(node.right);
                }
            }
            level_List.add(temp);
        }
        return level_List;
    }
}
```