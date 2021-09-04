---
title: "二叉搜索树的最近公共祖先"
date: 2021-09-04T15:54:27+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

示例 1:
> 输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8  
> 输出: 6  
> 解释: 节点 2 和节点 8 的最近公共祖先是 6。  

示例 2：
> 输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4  
> 输出: 2  
> 解释: 节点 2 和节点 4 的最近公共祖先是 2, 因为根据定义最近公共祖先节点可以为节点本身。

# 解法
## 三数组
仿照哈夫曼树，搜索两个结点，返回其搜索序列，左子树为0， 右子树为1  

他们的最近公共祖先，就是他们两个搜索序列最后一个重复的元素，该元素与前面序列组成的新序列就是该祖先的搜索序列

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
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        ArrayList<Integer> p_arr = new ArrayList<>();
        ArrayList<Integer> q_arr = new ArrayList<>();
        ArrayList<Integer> total_arr = new ArrayList<>();
        search(p_arr, root, p);
        search(q_arr, root, q);
        for (int i = 0; i < Math.min(p_arr.size(), q_arr.size()); i++) {
            if(p_arr.get(i) == q_arr.get(i)) {
                total_arr.add(p_arr.get(i));
            }
            else {
                break;
            }
        }
        TreeNode pos = root;
        for (int i = 0; i < total_arr.size(); i++) {
            if (total_arr.get(i) == 0) {
                pos = pos.left;
            }
            if (total_arr.get(i) == 1) {
                pos = pos.right;
            }
        }
        return pos;
    }

    private void search(ArrayList<Integer> arr, TreeNode root, TreeNode search_node) {
        while (root != null) {
            if (search_node.val == root.val) {
                return;
            }
            else if (search_node.val < root.val) {
                arr.add(0);
                root = root.left;
            }
            else {
                arr.add(1);
                root = root.right;
            }
        }
    }
}
```

## 一次遍历
如果两个结点的值均小于当前结点，则当前结点左移。反之右移。  

如果一大一小，则出现了分岔，这就是最近公共祖先，返回即可。

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while (root != null) {
            if (p.val < root.val && q.val < root.val) {
                root = root.left;
            }
            else if (p.val > root.val && q.val > root.val) {
                root = root.right;
            }
            else {
                return root;
            }
        }
        return root;
    }
}
```