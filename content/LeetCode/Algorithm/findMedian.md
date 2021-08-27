---
title: "数据流的中位数"
date: 2021-08-27T22:00:36+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---
# 题目描述
中位数是有序列表中间的数。如果列表长度是偶数，中位数则是中间两个数的平均值。

例如，

[2,3,4] 的中位数是 3

[2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

* void addNum(int num) - 从数据流中添加一个整数到数据结构中。
* double findMedian() - 返回目前所有元素的中位数。

# 解法
设置两个优先队列，一个 que_min 存储低于中位数的数，一个 que_max 存储高于中位数的数，que_min 从小到大，que_max 从大到小，约定第一个数进入que_min  

因此当总数为奇数时，中位数为 que_min；偶数时，为 que_min 和 que_max 的平均值  

```java
class MedianFinder {

    PriorityQueue<Integer> que_min;
    PriorityQueue<Integer> que_max;
    /** initialize your data structure here. */
    public MedianFinder() {
        que_min = new PriorityQueue<>((a, b) -> (b - a));
        que_max = new PriorityQueue<>((a, b) -> (a - b));
    }
    
    public void addNum(int num) {
        if (que_min.size() == 0 || num <= que_min.peek()) {
            que_min.offer(num);
            if (que_min.size() > que_max.size() + 1) { que_max.offer(que_min.poll()); }
        }
        else {
            que_max.offer(num);
            if (que_max.size() > que_min.size()) { que_min.offer(que_max.poll()); }
        }
    }
    
    public double findMedian() {
        if (que_min.size() > que_max.size()) { return que_min.peek(); }
        else { return (que_min.peek() + que_max.peek()) / 2.0; }
    }

}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```