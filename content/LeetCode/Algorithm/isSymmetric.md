---
title: "对称二叉树"
date: 2021-09-01T22:00:00+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个二叉树，检查它是否是镜像对称的。

例如：
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
上面这棵树是对称的  

但下面这棵树不是
```
    1
   / \
  2   2
   \   \
   3    3
```

# 解法
## 递归
判断一棵树是否是对称的，转为判断其左右子树是否是对称的，递归到终点。  

方法为：比较左子树和右子树，左子树的左子树和右子树的右子树+左子树的右子树和右子树的左子树，如果同为null，或者数值相等，认为是对称的。

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
    public boolean isSymmetric(TreeNode root) {
        return check(root, root);
    }

    private boolean check(TreeNode m, TreeNode n) {
        if (m == null && n == null) {
            return true;
        }
        if (m == null || n == null) {
            return false;
        }
        if (m.val != n.val) {
            return false;
        }
        if (check(m.left, n.right) && check(m.right, n.left)) {
            return true;
        }
        return false;
    }
}
```

## 迭代法
迭代法通常使用队列来代替递归：将左右子树传入队列，一次从队列取出两个元素，判断其是否满足对称条件，如果满足，则将第一个元素的左子树、第二个元素的右子树放入队列，然后再把第一个元素的右子树、第二个元素的左子树放入队列，然后依次比较，直到发现一次不匹配，或者队列为空
```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        Queue<TreeNode> que = new LinkedList<>();
        que.offer(root);
        que.offer(root);
        while (!que.isEmpty()) {
            TreeNode m = que.poll();
            TreeNode n = que.poll();
            if (m == null && n == null) {
                continue;
            }
            if ((m == null || n == null) || (m.val != n.val)) {
                return false;
            }

            que.offer(m.left);
            que.offer(n.right);

            que.offer(m.right);
            que.offer(n.left);
        }
        return true;
    }
}
```

## 数组
如果一个数镜像对称，则它的左深度优先遍历和右深度优先遍历出来的数组应该是一样的，将此特性转化为数组，分别建立两个深度优先遍历，比较产生的数组：注意，当root为null时，我们给数组赋予一个特殊值，否则会出错。

```java
class Solution {
    List<Integer> left_list = new ArrayList<>();
    List<Integer> right_list = new ArrayList<>();

    public boolean isSymmetric(TreeNode root) {
        if (root == null) {
            return true;
        }
        preOrderLeft(root);
        preOrderRight(root);
        if (left_list.size() != right_list.size()) {
            return false;
        }
        for (int i = 0; i < left_list.size(); i++) {
            if (left_list.get(i) != right_list.get(i)) {
                return false;
            }
        }
        return true;
    }

    private void preOrderLeft(TreeNode root) {
        if (root != null) {
            left_list.add(root.val);
            preOrderLeft(root.left);
            preOrderLeft(root.right);
        }
        else {
            left_list.add(-1);
        }
    }

    private void preOrderRight(TreeNode root) {
        if (root != null) {
            right_list.add(root.val);
            preOrderRight(root.right);
            preOrderRight(root.left);
        }
        else {
            right_list.add(-1);
        }
    }
}
```