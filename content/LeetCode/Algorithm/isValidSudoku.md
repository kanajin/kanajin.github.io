---
title: "有效的数独"
date: 2021-08-26T11:03:39+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
请你判断一个 9x9 的数独是否有效。只需要 根据以下规则 ，验证已经填入的数字是否有效即可。

* 数字 1-9 在每一行只能出现一次。
* 数字 1-9 在每一列只能出现一次。
* 数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。

数独部分空格内已填入了数字，空白格用 '.' 表示。

注意：

* 一个有效的数独（部分已被填充）不一定是可解的。
* 只需要根据以上规则，验证已经填入的数字是否有效即可。

# 解法
解答的突破口在于如何获得子矩阵的编号，参考[重塑矩阵](https://kanajin.github.io/leetcode/algorithm/matrixreshape/)中提到的二维矩阵一维化的思路，写出如下表达式：

matrix_index = (row/3)*3 + (column/3)

然后用哈希表存储每行、每列的元素，观察是否重复

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashMap<Character, Integer>[] row = new HashMap[9];
        HashMap<Character, Integer>[] column = new HashMap[9];
        HashMap<Character, Integer>[] matrix = new HashMap[9];

        for (int i = 0; i < 9; i++) {
            row[i] = new HashMap();
            column[i] = new HashMap();
            matrix[i] = new HashMap();
        }

        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    continue;
                }
                row[i].put(board[i][j], row[i].getOrDefault(board[i][j], 0)+1);
                column[j].put(board[i][j], column[j].getOrDefault(board[i][j], 0)+1);
                matrix[(i/3)*3+(j/3)].put(board[i][j], matrix[(i/3)*3+(j/3)].getOrDefault(board[i][j], 0)+1);

                if (row[i].get(board[i][j]) > 1 || column[j].get(board[i][j]) > 1 || matrix[(i/3)*3+(j/3)].get(board[i][j]) > 1) {
                    return false;
                }
            }
        }
        
        return true;
    }
}
```