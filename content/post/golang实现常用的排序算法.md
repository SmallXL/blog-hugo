---
title: "Golang实现常用的排序算法"
date: 2018-05-22T13:38:05+08:00
lastmod: 2018-05-22T13:38:05+08:00
draft: false
keywords: []
description: ""
tags: [golang,排序]
categories: [golang小实践]
author: "xlxing"

# you can close something for this content if you open it in config.toml.
comment: false
toc: false
# you can define another contentCopyright. e.g. contentCopyright: "This is an another copyright."
contentCopyright: false
reward: false
mathjax: false
---

<!--more-->
**以下排序算法的思想网上都可以搜到相对应的介绍，这里不再赘述，直接给出代码实现：**

```
package main

import (
	"fmt"
)

// 冒泡排序
func bubbleSort(s []int) {
	for i := 0; i < len(s); i++ {
		for j := i + 1; j < len(s); j++ {
			if s[i] > s[j] {
				s[i], s[j] = s[j], s[i]
			}
		}

	}
}

// 插入排序
func insertSort(s []int, n int) {
	//取出当前值，跟前面的值进行比较，直到找到比它小的值;然后重复
	for i := n; i < len(s); i++ {
		tmp := s[i]
		for j := i; j-n > 0 && s[j-n] > tmp; j -= n {
			s[j] = s[j-n]
			s[j-n] = tmp
		}
	}
}

// 希尔排序
func shellSort(s []int) {
	//分阶段的插入排序
	for n := len(s) / 2; n >= 1; n /= 2 {
		insertSort(s, n)
	}
}

// 选择排序
func selectSort(s []int) {
	selectMin := func(s []int, i int) int {
		for j := i + 1; j < len(s); j++ {
			if s[j] < s[i] {
				i = j
			}
		}
		return i
	}
	for i := 0; i < len(s); i++ {
		//首先筛选出剩余数组中最小值的索引
		minIndex := selectMin(s, i)
		//进行比较，如果不是最小值则交换
		if i != minIndex {
			s[i], s[minIndex] = s[minIndex], s[i]
		}
	}
}

// 快速排序
func quickSort(s []int, left, right int) {
	sort := func(s []int, low, high int) int {
		tmp := s[low]
		for low < high {
			//注意这里的顺序，必须先操作high
			for low < high && s[high] >= tmp {
				high--
			}
			s[low], s[high] = s[high], s[low]
			for low < high && s[low] <= tmp {
				low++
			}
			s[low], s[high] = s[high], s[low]
		}
		return low
	}
	if left < right {
		index := sort(s, left, right)
		quickSort(s, left, index-1)
		quickSort(s, index+1, right)
	}
}

// 堆排序
func heapSort(s []int) {
	heapAdjust := func(s []int, parent, len int) {
		var i int
		for 2*parent+1 < len {
			lchild := 2*parent + 1
			rchild := lchild + 1
			i = lchild
			//取出两个叶子节点中最大的一个
			if rchild < len && s[rchild] > s[lchild] {
				i = rchild
			}
			//如果最大的叶子节点大于父节点则交换，否则推出循环
			if s[i] > s[parent] {
				s[parent], s[i] = s[i], s[parent]
				parent = i
			} else {
				break
			}
		}
	}
	//从最后一个非叶子节点开始调整(len(s)/2-1)
	for i := len(s)/2 - 1; i >= 0; i-- {
		heapAdjust(s, i, len(s))
	}
	for i := len(s) - 1; i > 0; i-- {
		//将第一个和最后一个交换然后继续调整堆
		s[0], s[i] = s[i], s[0]
		heapAdjust(s, 0, i)
	}
}

func main() {
	s := []int{1, 3, 5, 7, 9, 2, 4, 8, 10, 6}
	// 冒泡排序
	fmt.Println("冒泡排序:")
	fmt.Println(s)
	bubbleSort(s)
	fmt.Println(s)

	// 插入排序
	fmt.Println("插入排序:")
	s = []int{1, 3, 5, 7, 9, 2, 4, 8, 10, 6}
	fmt.Println(s)
	insertSort(s, 1)
	fmt.Println(s)

	// 希尔排序
	fmt.Println("希尔排序:")
	s = []int{1, 3, 5, 7, 9, 2, 4, 8, 10, 6}
	fmt.Println(s)
	shellSort(s)
	fmt.Println(s)

	// 选择排序
	fmt.Println("选择排序:")
	s = []int{1, 3, 5, 7, 9, 2, 4, 8, 10, 6}
	fmt.Println(s)
	selectSort(s)
	fmt.Println(s)

	// 快速排序
	fmt.Println("快速排序:")
	s = []int{1, 3, 5, 7, 9, 2, 4, 8, 10, 6}
	fmt.Println(s)
	quickSort(s, 0, len(s)-1)
	fmt.Println(s)

	// 堆排序
	fmt.Println("堆排序:")
	s = []int{1, 3, 5, 7, 9, 2, 4, 8, 10, 6}
	fmt.Println(s)
	heapSort(s)
	fmt.Println(s)
}
```
运行结果：

```
冒泡排序:
[1 3 5 7 9 2 4 8 10 6]
[1 2 3 4 5 6 7 8 9 10]
插入排序:
[1 3 5 7 9 2 4 8 10 6]
[1 2 3 4 5 6 7 8 9 10]
希尔排序:
[1 3 5 7 9 2 4 8 10 6]
[1 2 3 4 5 6 7 8 9 10]
选择排序:
[1 3 5 7 9 2 4 8 10 6]
[1 2 3 4 5 6 7 8 9 10]
快速排序:
[1 3 5 7 9 2 4 8 10 6]
[1 2 3 4 5 6 7 8 9 10]
堆排序:
[1 3 5 7 9 2 4 8 10 6]
[1 2 3 4 5 6 7 8 9 10]
```
