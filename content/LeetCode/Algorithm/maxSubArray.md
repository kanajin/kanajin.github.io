---
title: "最大子序和"
date: 2021-08-23T16:34:59+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

# 解法
*假设数组名nums*
## 动态规划法
假设以角标为 i 的元素结尾的子序列中，最大和的值为f(i)；那么 f(i) 有以下两种取值可能：
1. f(i-1)+nums[i]，即上一个最大子序列和 f(i-1) 的值加上 nums[i] 比 nums[i] 本身大
2. nums[i]，即 f(i-1) + nums[i] < nums[i]

即 f(i) = max{f(i-1) + nums[i], nums[i]}

因此我们需要一个变量来存储f(i-1)，并从0遍历数组不断动态规划

```java
class Solution {

    public int maxSubArray(int[] nums) {
        //在初始状态下，pre从0开始规划
        int pre = 0;
        //假设数组的第一个子集为最大和
        int max = nums[0];
        for(int i : nums){
            //将思路翻译为代码
            pre = Math.max(pre + i, i);
            //若f(i)比存储的最大值大，更新他
            max = Math.max(pre, max);
        }
        return max;
    }

}
```

## 分治法
首先分治算法的基本架构：将大问题分解为两个或多个同样模型，但是规模较小的小问题，然后基于小问题的解得出原问题的解。
### 设计过程
[参考网址](https://leetcode-cn.com/problems/maximum-subarray/solution/divide-and-conquer-fen-zhi-suan-fa-she-j-4sbv/)  
假定我们现在期望求区间 M:[l,r] 上的最大子序列 M.m_sum, 按照分治算法的思路，我们会把这个区间分为 L:[l,m] 和 R:[m+1,r] 两部分。那么 M:[l,r] 上的最大子序列出现的位置共有以下三种可能：
1. M.m_sum 完全在 L 内，即 M.m_sum = L.m_sum
2. M.m_sum 完全在 R 内，即 M.m_sum = R.m_sum
3. M.m_sum 横跨两个区间

对于第三种情况，则 M.m_sum 必定从左序列的某个右边界延续到右序列的某个左边界，容易得出结论，该序列必定是 L 序列以右边界结尾的最大子序列，与 R 序列的以左边界开头的最大子序列之和，因此我们需要维护 l_sum 和 r_sum 两个变量，并可以得出以下结论：

> M.m_sum = Max{Max{L.m_sum, R.m_sum}, L.r_sum + R.l_sum}

接下来分析如何维护 l_sum 与 r_sum，由于是对偶的，只需分析一边即可：  

对于 M 序列，其 l_sum 必定从左边界开始  
> 如果没有蔓延到中点右边，则有 M.l_sum = L.l_sum
> 如果蔓延到了中点右边，则有 M.l_sum = 整个 L 序列 + R.l_sum

因此，我们还需要维护一个表示整个序列的变量 t_sum。维护这个变量的方式很简单，当分治递归到最底层时， t_sum 即为单元素序列的元素值  

至此，我们一共有了四个变量，分别为：
> l_sum：当前序列以左边界为起点的最大子序列和
> r_sum：当前序列以右边界为终点的最大子序列和
> m_sum：当前序列的最大子序列和
> t_sum：当前序列所有元素和

这四个变量，开始分析算法

当分治法递归到底层时，每个序列只有一个元素 n，因此，我们可以另这四个变量都等于 n，并跳出本次递归，开始合并  

考察两个序列 L、R 合并成的新序列 M，考察其四个变量：
> l_sum：M.l_sum = Max{L.l_sum, L.t_sum + R.l_sum}
> r_sum: M.r_sum = Max{R.r_sum, l.r_sum + R.t_sum}
> m_sum: M.m_sum = Max{Max{L.m_sum, R.m_sum}, L.r_sum + R.l_sum}
> t_sum: M.t_sum = L.t_sum + R.t_sum

维护好每个子序列的四个变量后，逐渐合并递归即可得到整个数组的四个变量，可以开始着手写代码了

```java
class Solution {

    public class ArrInfo {

        //四个变量
        int l_sum, r_sum, m_sum, t_sum;

        //初始化四个变量
        public ArrInfo(int l_sum, int r_sum, int m_sum, int t_sum) {
            this.l_sum = l_sum;
            this.r_sum = r_sum;
            this.m_sum = m_sum;
            this.t_sum = t_sum;
        }

    }

    //算法总接口
    public int maxSubArray(int[] nums) {
        ArrInfo sol = get(nums, 0, nums.length-1);
        return sol.m_sum;
    }

    //合
    private ArrInfo merge(ArrInfo l_sub, ArrInfo r_sub) {
        //合并后维护四个变量
        int l_sum = Math.max(l_sub.l_sum, l_sub.t_sum + r_sub.l_sum);
        int r_sum = Math.max(r_sub.r_sum, l_sub.r_sum + r_sub.t_sum);
        int m_sum = Math.max(Math.max(l_sub.m_sum, r_sub.m_sum), l_sub.r_sum + r_sub.l_sum);
        int t_sum = l_sub.t_sum + r_sub.t_sum;
        return new ArrInfo(l_sum, r_sum, m_sum, t_sum);
    }

    //分
    private ArrInfo get(int[] nums, int l, int r) {
        //递归到底层时，另每个变量都为该元素
        if (l == r) { return new ArrInfo(nums[l], nums[l], nums[l], nums[l]); }
        //获得序列中点
        int m = (l + r) >> 1;
        //递归左序列
        ArrInfo l_sub = get(nums, l, m);
        //递归右序列
        ArrInfo r_sub = get(nums, m+1, r);
        //返回合并序列
        return merge(l_sub, r_sub);
    }

}
```