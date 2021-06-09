---
title: "快速排序"
date: 2021-06-09T17:31:17+08:00
draft: false
categories: ["数据结构"]
enableMathJax: ture
---

# 简介
**快速排序**(Quicksort)，又称**分区交换排序**(partition-exchange sort)，简称**快排**。在平均情况下，排序$n$个项目要$O(n\log_{}{n} )$次比较。在最坏情况下则需要$O(n^2)$次比较，但这种情况并不常见。事实上，快速排序通常比其他算法更快，因为它的内部循环(inner loop)可以在大部分的架构上很有效率地达成。
# 思路
* 在数组中选择一个锚点p，一般为数组的第一个数，把数组分为 <p 和 >=p 两部分。  
* ~~当然你可以选择数组中的任意一个数，但为了自己思路以及代码的简明，我们选择以数组的第一个数为锚点~~
* 从将数组划分为以2个元素为一组的子数组开始，递归地完成上面的步骤。
# 做法：
* 首先记录a[0]的值为p,然后设定一个变量m用来做分界线(a[1]~a[m-1]用来存放小于p的数，a[m]~a[n]用于存放大于等于p的数)。  
* 显而易见的，我们需要一个循环参数用来探索数组，将它指向的数与p作比较，根据比较结果判断这个数应该放在1~m-1中还是m~n中。我们定义这个参数为k，随着`k++`，它探索着数组。  
图解：  
![partition1](/QuickSort/partition1.png)  
当a[k]大于等于p时，什么也不做，k继续向右移动。  
![partition2](/QuickSort/partition2.png)  
当a[k]小于p时，左边区域的成员应该增加，所以我们将m的值+1，把右边一个大于p的数囊括进来，然后让a[k]和它交换位置，这样就完成了分类。

# 代码
```C
//交换函数
void swap(int *a, int *b){
    int temp = *a;
    *a = *b;
    *b = temp;
}
//执行段，执行上述图解的内容
int quick(int *a, int start, int end){
    int p = a[start];
    int m =start;
    for(int k = start+1; k <= end; k++){
        if(a[k] <= p){
            m++;
            swap(&a[m], &a[k]);
        }
        else;
    }
    swap(&a[start], &a[m]);
    return m;
}
//递归段，将数组分解成更小的子数组，从只含两个元素的子数组开始排序，直到最后将整个数组排序
void quick_sort(int *a, int start, int end){
    int m = quick(a, start, end);
//跳出条件
    if(m > end){
        return;
    }
    quick_sort(a, start, m-1);
    quick_sort(a, m + 1, end);
}
```