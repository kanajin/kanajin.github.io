---
title: "重塑矩阵"
date: 2021-08-26T00:42:11+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: true
---

# 题目描述
在 MATLAB 中，有一个非常有用的函数 reshape ，它可以将一个 m x n 矩阵重塑为另一个大小不同（r x c）的新矩阵，但保留其原始数据。

给你一个由二维数组 mat 表示的 m x n 矩阵，以及两个正整数 r 和 c ，分别表示想要的重构的矩阵的行数和列数。

重构后的矩阵需要将原始矩阵的所有元素以相同的 行遍历顺序 填充。

如果具有给定参数的 reshape 操作是可行且合理的，则输出新的重塑矩阵；否则，输出原始矩阵。  
# 解法
二维矩阵一维化：  
对于一个行为 r, 列为 c 二位数组，我们可以将其下角标 i, j 映射为一维的 x，映射规则为：  

$x = ci + j$  

要转化回原始坐标时，关系如下：  

1. $i = x / c$
2. $j = x \% c$

原因是：  

因为 j 必定小于 c，因此做除法时一定会被舍去，只剩下 i；而取余时必定只留下 j

```java
class Solution {
    public int[][] matrixReshape(int[][] mat, int r, int c) {
        if (mat.length * mat[0].length != r*c) { return mat; }
        else {
            int[][] reshape = new int[r][c];
            for (int i = 0; i < r * c; i++) {
                reshape[i/c][i%c] = mat[i/mat[0].length][i%mat[0].length];
            }
            return reshape;
        }
    }
}
```