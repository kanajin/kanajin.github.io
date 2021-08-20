---
title: "两数之和"
date: 2021-08-20T19:14:59+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

# 解法
## 暴力解法
暴力解法是最直观的思路，遍历所有的元素组合，判断其加和是否等于目标值
```Java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] arr = new int[2];
        for(int i = 0; i < nums.length-1; i++){
            for(int j = i + 1; j < nums.length; j++){
                if(nums[i] + nums[j] == target){
                    arr[0] = i;
                    arr[1] = j;
                }
            }
        }
        return arr;
    }
}
```
## 哈希表解法
暴力解法的问题在于遍历了两次数组，即遍历一次找出第一个元素，遍历第二次寻找是否存在与之加和等于目标值的元素。  
事实上，可在第一次遍历时，同时将元素写入哈希表，当遇到与他互补的元素时，便可以从哈希表中找到相应值，从而将“找互补数”的操作时间复杂度变为$O(1)$。
```Java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> dic = new HashMap<>();
        for(int i = 0; i < nums.length; i++){
            if(dic.containsKey(target - nums[i])){
                return new int[] {i, dic.get(target - nums[i])};
            }
            dic.put(nums[i], i);
        }
        return new int[2];
    }
}
```
**这样return也是可以的*