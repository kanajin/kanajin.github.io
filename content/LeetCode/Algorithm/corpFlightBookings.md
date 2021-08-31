---
title: "航班预定统计"
date: 2021-08-31T16:14:26+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

[原网址](https://leetcode-cn.com/problems/corporate-flight-bookings/)

# 题目描述
这里有 n 个航班，它们分别从 1 到 n 进行编号。

有一份航班预订表 bookings ，表中第 i 条预订记录 bookings[i] = [firsti, lasti, seatsi] 意味着在从 firsti 到 lasti （包含 firsti 和 lasti ）的 每个航班 上预订了 seatsi 个座位。

请你返回一个长度为 n 的数组 answer，其中 answer[i] 是航班 i 上预订的座位总数。

示例 1：  
```
输入：bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
输出：[10,55,45,25,25]
解释：
航班编号        1   2   3   4   5
预订记录 1 ：   10  10
预订记录 2 ：       20  20
预订记录 3 ：       25  25  25  25
总座位数：      10  55  45  25  25
因此，answer = [10,55,45,25,25]
```

示例 2：
```
输入：bookings = [[1,2,10],[2,2,15]], n = 2
输出：[10,25]
解释：
航班编号        1   2
预订记录 1 ：   10  10
预订记录 2 ：       15
总座位数：      10  25
因此，answer = [10,25]
```

# 解法
## 差分法：  

差分数组的性质：  
对一个数组如 [1,2,3,4]，其差分数组为 [1,1,1,1]，即该位元素减去前一位元素形成的新数组。要从差分数组变回原数组，只需从左到右依次加回去即可。  

若要在差分数组的 [l,r] 区间上增加一个增量 ins，只需将 l 位元素增加 ins，对应 r+1 位元素减去 ins，就可以完成操作。  

利用这种性质，本体可以转换成制作差分数组，可以在 O(1) 的复杂度内完成数值加减。

```java
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] answer = new int[n];
        for (int[] books : bookings) {
            answer[books[0]-1] += books[2];
            if (books[1] < n) {
                answer[books[1]] -= books[2];
            }
        }
        for (int i = 1; i < n; i++) {
            answer[i] += answer[i-1];
        }
        return answer;
    }
}
```