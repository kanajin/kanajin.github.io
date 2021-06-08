---
title: "选择排序"
date: 2021-06-07T11:00:36+08:00
draft: false
categories: ["数据结构"]
---

# 选择排序：  
遍历一遍数组，选择最小的数（做法：先指定遍历的第一个数为最小，遍历一遍数组，如果发现比他小的，则记录这个最小值），将最小的数与遍历的第一个数对调，完成一次操作。然后从下一个数开始遍历，直到完成。
## 内循环：  
遍历一遍数组，找到最小值（第n次遍历就从数组第n个数开始），然后将最小值与遍历的第一个数对调
## 外循环：  
指定内循环次数，值为数组长度。  
## 代码：
```C
void sort(int *a, int size){
    for(int i = 0; i < size; i++){
        int temp = a[i];
        int probe = i;
        for(int j = i + 1; j < size; j++){
            if(a[j] < temp){
                temp = a[j];
                probe = j;
            }
        }
        swap(&a[i], &a[probe]);
    }
}
```