---
title: "排序算法"
date: 2023-01-07T10:38:35+08:00
draft: false
categories: ["算法"]
tags: ["算法"]
---

## 冒泡排序

重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来，因小元素会经由交换慢慢“浮”到数列的顶端而得名

### 代码

```go
func bubbleSort[T constraints.Ordered](arr []T) {
	for i := 0; i < len(arr); i++ {
		for j := i; j < len(arr); j++ {
			if arr[i] > arr[j] {
				arr[i], arr[j] = arr[j], arr[i] //swap
			}
		}
	}
}
```

## 插入排序

从第二个元素(index=1)开始，不断向前“插队”到第一个比他小的元素之后

### 代码

```go
func insertionSort[T constraints.Ordered](arr []T) {
	for i := 1; i < len(arr); i++ {
		j := i - 1
		key := arr[i]
		for ; j >= 0 && arr[j] > key; j-- {
			arr[j+1] = arr[j]
		}
		arr[j+1] = key
	}
}
```

## 选择排序

反复遍历数组，每次选出最小的元素移动至合适的位置

### 代码

```go
func selectionSort[T constraints.Ordered](arr []T) {
	for i := 0; i < len(arr)-1; i++ {
		min := i
		for j := i + 1; j < len(arr); j++ {
			if arr[j] < arr[i] {
				min = j
			}
		}
		if min != i {
			swap(arr, i, min)
		}
	}
}
```

## 快速排序

1. 选出一个基准值
2. 将数组调整为两部分，左边元素比基准值小，右边元素比基准值大
3. 将两部分数组递归地执行1、2步骤，最后完成排序

### 代码

```go
func quickSort[T constraints.Ordered](arr []T) {    //算法接口
	if len(arr) < 2 {
		return
	}
	_quick(arr, 0, len(arr))
}

func _quick[T constraints.Ordered](arr []T, start, end int) {   //递归段
	if start >= end {
		return
	}

	idx := partition(arr, start, end)
	_quick(arr, start, idx-1)   //分成左右两部分
	_quick(arr, idx, end)
}

func partition[T constraints.Ordered](arr []T, start, end int) int {    //排序段，以段落的第一个元素arr[start]为基准
	idx := start + 1

	for i := idx; i < end; i++ {
		if arr[i] < arr[start] {
			arr[i], arr[idx] = arr[idx], arr[i] //将比基准值小的数放到数组左边
			idx++
		}
	}
    arr[start], arr[idx-1] = arr[idx-1], arr[start] //将基准值放在中间
	return idx  //返回基准值的下标
}
```

## 堆排序

1. 先将数组构建为大根堆
2. 将堆顶元素与末位元素对调，即将当前最大元素放到正确的位置，将调整后的大元素移出堆，不参与调整
3. 重新将调整后的堆转化为大根堆
4. 重复操作，直到所有元素都被移出堆

### 代码

```go
func heapSort[T constraints.Ordered](arr []T) {
	arrLen := len(arr)
	buildMaxHeap(arr, arrLen)
	for i := arrLen - 1; i >= 0; i-- {  //不断将堆顶元素交换到“末位”
        arr[0], arr[i] = arr[i], arr[0]
		arrLen--    //将末位元素移出堆
		heapify(arr, 0, arrLen)
	}
}

func buildMaxHeap[T constraints.Ordered](arr []T, arrLen int) { //构建堆
	for i := arrLen / 2; i >= 0; i-- {  //从最后一个非叶节点开始
		heapify(arr, i, arrLen)
	}
}

func heapify[T constraints.Ordered](arr []T, idx, arrLen int) { //调整堆
	l := idx*2 + 1  //左子树
	r := idx*2 + 2  //右子树
	largest := idx

	if l < arrLen && arr[l] > arr[largest] {
		largest = l
	}
	if r < arrLen && arr[r] > arr[largest] {
		largest = r
	}
	if largest != idx {
        arr[idx], arr[largest] = arr[largest], arr[idx]
		heapify(arr, largest, arrLen)
	}
}
```
