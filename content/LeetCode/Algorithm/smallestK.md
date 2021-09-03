---
title: "最小K个数"
date: 2021-09-03T10:26:19+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
设计一个算法，找出数组中最小的k个数。以任意顺序返回这k个数均可。

# 解法
## 排序
先对数组进行排序，然后返回前k个即可  
```java
class Solution {
    public int[] smallestK(int[] arr, int k) {
        sort(arr, arr.length);
        int[] smallest_k = new int[k];
        for (int i = 0; i < k; i++) {
            smallest_k[i] = arr[i];
        }
        return smallest_k;
    }

    private void swap (int[] arr, int a, int b) {
        int temp = arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }

    private void siftDown (int[] arr, int p, int len) {
        int lc = 2*p + 1;
        int rc = lc + 1;
        int max = p;
        if (lc < len && arr[lc] > arr[max]) {
            max = lc;
        }
        if (rc < len && arr[rc] > arr[max]) {
            max = rc;
        }
        if (max != p) {
            swap(arr, max, p);
            siftDown(arr, max, len);
        }
    }

    private void buildMaxHeap(int[] arr) {
        for (int i = arr.length/2; i >= 0; i--) {
            siftDown(arr, i, arr.length);
        }
    }

    private void sort(int[] arr, int sift_len) {
        buildMaxHeap(arr);
        for (int i = arr.length-1; i >= 0; i--) {
            swap(arr, 0, i);
            siftDown(arr, 0, --sift_len);
        }
    }
}
```
或者调用接口
```java
class Solution {

    public int[] smallestK(int[] arr, int k) {
        Arrays.sort(arr);
        int[] smallest_k = new int[k];
        for (int i = 0; i < k; i++) {
            smallest_k[i] = arr[i];
        }
        return smallest_k;
    }
}
```

## 快排思路
快排为不断将数组分治，找到一个分界点，左侧皆为小于分界点值的元素，右侧皆为大于分界点值的元素。  

当分界点的下标为k时，表示左侧k个元素即为需要返回的元素。  

```java
class Solution {

    public int[] smallestK(int[] arr, int k) {
        if (k == 0) {
            return new int[0];
        }
        int[] smallest_k = new int[k];
        find(arr, 0, arr.length, k);
        for (int i = 0; i < k; i++) {
            smallest_k[i] = arr[i];
        }
        return smallest_k;
    }

    private void swap(int[] arr, int a, int b) {
        int temp = arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }

    private int divide(int[] arr, int start, int end) {
        int p = arr[start];
        int m = start;
        int k = start+1;
        for (; k < end; k++) {
            if (arr[k] <= p) {
                m++;
                swap(arr, m, k);
            }
        }
        swap(arr, start, m);
        return m;
    }

    private int find(int[] arr, int start, int end, int k) {
        int m = divide(arr, start, end);
        while (m != k) {
            if (m < k) {
                m = find(arr, m+1, end, k);
            }
            if (m > k) {
                m = find(arr, start, m, k);
            }
        }
        return m;
    }
}
```

## 优先队列
创建一个队头为最大元素的优先队列，存入k个数，然后遍历数组，如果比队头小，就换掉队头  

```java
class Solution {

    public int[] smallestK(int[] arr, int k) {
        if (k == 0) {
            return new int[0];
        }
        PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> (b - a));
        for (int i = 0; i < k; i++) {
            pq.offer(arr[i]);
        }
        for (int i = k; i < arr.length; i++) {
            if (arr[i] < pq.peek()) {
                pq.poll();
                pq.offer(arr[i]);
            }
        }
        int[] smallest_k = new int[k];
        for (int i = 0; i < k; i++) {
            smallest_k[i] = pq.poll();
        }
        return smallest_k;
    }
}
```