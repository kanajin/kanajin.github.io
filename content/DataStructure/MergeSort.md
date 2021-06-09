---
title: "归并排序"
date: 2021-06-07T18:07:03+08:00
draft: false
categories: ["数据结构"]
enableMathJax: true
---

本文摘抄自`developer1024`的知乎文章[【算法】排序算法之归并排序](https://zhuanlan.zhihu.com/p/124356219)。
# 简介
归并排序使用的是“分而治之”的方法，思路简单，速度仅次于快速排序，一般用于总体无序但各子项相对有序的数组。  
# 1、基本思想  
归并排序使用的是分治思想，分治模式在每一层递归上有三个步骤。  
* 分解（Divide）：将n个元素分成 $\frac{2}{n}$个元素的子序列。
* 解决（Conquer）：用合并排序法对两个子序列递归地排序。
* 合并（Combine）：合并两个已排序的子序列以得到排序结果。

# 2、实现逻辑
## 2.1、迭代法
1. 申请空间，使其大小为两个已经排序数列之和，该空间用来存放合并后的数列。
2. 设定两个指针，最初位置分别为两个已经排序子数列的起始位置。
3. 比较两个指针所指向元素，选择相对小的元素放到合并空间，并移动指针到下一个位置。
4. 重复步骤3，直到某一指针打到子数列尾。
5. 将另一序列剩下的所有元素直接复制到合并序列尾。

## 2.2、递归法
1. 将序列每相邻两个数字进行归并操作，形成$floor(\frac{n}{2})$个序列，排序后每个序列将包含两个元素。
2. 将上述序列再次归并，形成$floor(\frac{n}{4})$个序列，每个序列包含四个元素。
3. 重复步骤2，直到所有元素排序完毕。

# 3、代码实现
## 迭代法：
```C
int min(int x, int y) {
    return x < y ? x : y;
}
void merge_sort(int arr[], int len) {
    int* a = arr;
    int* b = (int*) malloc(len * sizeof(int));
    int seg, start;
//外循环：步长成倍提升，并通过内循环将主数列从分成n个子数列，下次循环分成n/2个...直到只分成2个
    for (seg = 1; seg < len; seg += seg) {
//内循环：创建子数列，并将子数列排序。
        for (start = 0; start < len; start += seg + seg) {
            int low = start, mid = min(start + seg, len), high = min(start + seg + seg, len);
            int k = low;
            int start1 = low, end1 = mid;
            int start2 = mid, end2 = high;
            while (start1 < end1 && start2 < end2)
                b[k++] = a[start1] < a[start2] ? a[start1++] : a[start2++];
            while (start1 < end1)
                b[k++] = a[start1++];
            while (start2 < end2)
                b[k++] = a[start2++];
        }
        int* temp = a;
        a = b;
        b = temp;
    }
    if (a != arr) {
        int i;
        for (i = 0; i < len; i++)
            b[i] = a[i];
        b = a;
    }
    free(b);
}
```
## 递归法：
```C
void merge_sort_recursive(int *a, int *b, int start, int end){
    if(start >= end){
        return;
    }
    int mid = ((end - start) / 2) + start;
    int start1 = start;
    int end1 = mid;
    int start2 = mid +1;
    int end2 = end;
    merge_sort_recursive(a, b, start1, end1);
    merge_sort_recursive(a, b, start2, end2);
    int k = start;
    while (start1 <= end1 && start2 <= end2)
    {
        if(a[start1] < a[start2]){
            b[k] = a[start1];
            start1++;
        }
        else{
            b[k] = a[start2];
            start2++;
        }
        k++;
    }
    while (start1 <= end1)
    {
        b[k] = a[start1];
        k++;
        start1++;
    }
    while (start2 <= end2)
    {
        b[k] = a[start2];
        k++;
        start2++;
    }
    for(k = start; k <= end; k++){
        a[k] = b[k];
    }
}
```