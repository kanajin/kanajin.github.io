---
title: "数组中的第K个最大元素"
date: 2021-08-20T19:28:15+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

# 解法
## 优先队列
时间复杂度最小的做法为优先队列，出队K次即可
```Java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        return get(nums, k);
    }

    private void swap(int[] arr, int a, int b){
        int temp = arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }

    //维护最大堆
    private void siftDown(int[] arr, int p, int len){
        int lc = 2*p + 1;
        int rc = 2*p + 2;
        int max = p;
        if(lc < len && arr[lc] > arr[max]) { max = lc; } //此处arr[max]易错
        if(rc < len && arr[rc] > arr[max]) { max = rc; }
        if(max != p){
            swap(arr, max, p);
            siftDown(arr, max, len);
        }
    }

    //创建最大堆
    private void buildMaxHeap(int[] arr, int len){
        for(int i = len/2; i >= 0; i--){
            siftDown(arr, i, len);
        }
    }

    //删除首元素，并重新构建
    private void delHead(int[] arr, int len){
        swap(arr, 0, len-1);
        siftDown(arr, 0, len-1);
    }

    //弹出首元素，由于本题无法用this数组，只能手动管理长度
    private int pop(int[] arr, int len){
        int temp = arr[0];
        delHead(arr, len);
        return temp;
    }

    //算法实现
    private int get(int[] arr, int k){
        int len = arr.length;
        buildMaxHeap(arr, len);
        int temp = pop(arr, len);
        len--;
        for(int i = 1; i < k; i++){
            temp = pop(arr, len);
            len--;
        }
        return temp;
    }
}
```

## Api Player
没什么好说的，工作量downdowndown
```Java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        Arrays.sort(nums);
        return nums[nums.length-k];
    }
}
```