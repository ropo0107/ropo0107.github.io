---
title:  LeetCode排序
categories: [杂记]
tags:   [leetcode]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-03-05 17:51:55
---

leetcode中的排序问题

# 冒泡排序

## 思想
冒泡排序要对一个列表多次重复遍历。它要比较相邻的两项，并且交换顺序排错的项。每对 列表实行一次遍历，就有一个最大项排在了正确的位置。大体上讲，列表的每一个数据项都会在 其相应的位置 “冒泡”。如果列表有 n 项，第一次遍历就要比较 n-1 对数据。需要注意，一旦列 表中最大(按照规定的原则定义大小)的数据是所比较的数据对中的一个，它就会沿着列表一直 后移，直到这次遍历结束。

## 动画
![冒泡](https://pic4.zhimg.com/v2-33a947c71ad62b254cab62e5364d2813_b.webp)

## 代码
```python
def bubbleSort(alist):
    n = len(alist)
    for i in range(n-1, 0, -1):
        for j in range(0, i):
            if alist[j] > alist[j+1]:
                alist[j], alist[j+1] = alist[j+1], alist[j]
    return alist
```

## 优化点

冒泡排序必须要在最终位置找到之前不断交换数据项，所以它经常被认为是最低效的排序方法。但是，由于冒泡排序要遍历整个未排好的部分，它可以做一些大多数排序方法做不到的事。尤其是如果在整个排序过程中没有交换，我们就可断定列表已经排好。因此可改良冒泡排序，使其在已知列表排好的情况下提前结束。这就是说，如果一个列表只需要几次遍历就可排好，冒泡排序就占有优势：它可以在发现列表已排好时立刻结束。

## 冒泡排序优化
```python
def bubbleSort(alist):
    n = len(alist)
    exchange = False
    for i in range(n-1, 0, -1):
        for j in range(0, i):
            if alist[j] > alist[j+1]:
                alist[j], alist[j+1] = alist[j+1], alist[j]
                exchange = True
        # 如果发现整个排序过程中没有交换，提前结束
        if not exchange:
            break
    return alist
```

# 选择排序

## 思想
选择排序提高了冒泡排序的性能，它每遍历一次列表只交换一次数据，即进行一次遍历时找 到最大的项，完成遍历后，再把它换到正确的位置。和冒泡排序一样，第一次遍历后，最大的数据项就已归位，第二次遍历使次大项归位。这个过程持续进行，一共需要 n-1 次遍历来排好 n 个数据，因为最后一个数据必须在第 n-1 次遍历之后才能归位。

## 动图
![](https://pic1.zhimg.com/v2-1c7e20f306ddc02eb4e3a50fa7817ff4_b.webp)

## 代码

```python
# 选择排序
def selectionSort(alist):
    n = len(alist)

    for i in range(n - 1):
        # 寻找[i,n]区间里的最小值
        min_index = i
        for j in range(i+1, n):
            if alist[j] < alist[min_index]:
                min_index = j
        alist[i], alist[min_index] = alist[min_index], alist[i]
    return alist
```

# 插入排序

## 思想

插入排序的算法复杂度仍然是 [公式]，但其工作原理稍有不同。它总是保持一个位置靠前的 已排好的子表，然后每一个新的数据项被 “插入” 到前边的子表里，排好的子表增加一项。我们认为只含有一个数据项的列表是已经排好的。每排后面一个数据（从 1 开始到 n-1），这 个的数据会和已排好子表中的数据比较。比较时，我们把之前已经排好的列表中比这个数据大的移到它的右边。当子表数据小于当前数据，或者当前数据已经和子表的所有数据比较了时，就可 以在此处插入当前数据项。

## 动图
![](https://pic3.zhimg.com/v2-8a97a82ab39183f3177e99b5bb74e55e_b.webp)

## 代码
```python
# 插入排序
def insertionSort(alist):
    for i in range(1,len(alist)):
        currentvalue=alist[i]
        position=i
        while alist[position-1]>currentvalue and position>0:
            alist[position]=alist[position-1]
            position=position-1
        alist[position]=currentvalue
    return alist
```

# 希尔排序

## 思想

希尔排序有时又叫做 “缩小间隔排序”，它以插入排序为基础，将原来要排序的列表划分为一些子列表，再对每一个子列表执行插入排序，从而实现对插入排序性能的改进。划分子列的特定方法是希尔排序的关键。我们并不是将原始列表分成含有连续元素的子列，而是确定一个划分列表的增量 “i”，这个i更准确地说，是划分的间隔。然后把每间隔为i的所有元素选出来组成子列表，然后对每个子序列进行插入排序，最后当 i=1 时，对整体进行一次直接插入排序。

## 动图
![](https://pic3.zhimg.com/v2-f14e4169ff39bad42c3dd6c385ad9c72_b.webp)


## 代码
```python
#希尔排序
def shellSort(alist):
    n = len(alist)
    gap = n // 2
    while gap > 0:
        for i in range(gap):
            gapInsetionSort(alist, i, gap)
        gap = gap // 2
    return alist

# # start子数列开始的起始位置， gap表示间隔

def gapInsetionSort(alist,startpos,gap):
    #希尔排序的辅助函数
    for i in range(startpos+gap,len(alist),gap):
        position=i
        currentvalue=alist[i]

        while position>startpos and alist[position-gap]>currentvalue:
            alist[position]=alist[position-gap]
            position=position-gap
        alist[position]=currentvalue
```

# 归并排序

## 思想

归并排序是一种递归算法，它持续地将一个列表分成两半。如果列表是空的或者 只有一个元素，那么根据定义，它就被排序好了（最基本的情况）。如果列表里的元素超过一个，我们就把列表拆分，然后分别对两个部分调用递归排序。一旦这两个部分被排序好了，然后就可以对这两部分数列进行归并了。归并是这样一个过程：把两个排序好了的列表结合在一起组合成一个单一的有序的新列表。有自顶向下（递归法）和自底向上的两种实现方法。

## 动图
![](https://pic3.zhimg.com/v2-cdda3f11c6efbc01577f5c29a9066772_b.webp)

## 代码
自顶向下（递归法）方法实现
```python
# 归并排序
def mergeSort(alist):
    n = len(alist)
    __mergeSort(alist, 0, n-1)
    return alist

# 对arr[l...r]的范围进行排序
def __mergeSort(alist, start, end):
    #当数列的大小比较小的时候，数列近乎有序的概率较大
    if (end-start <= 15):
        insertionSortHelp(alist, start, end)
        return

    if start >= end:
        return
    # 存在风险，start+end可能越界
    mid = (start + end) // 2
    # mid = start + (end - start) // 2
    __mergeSort(alist, start, mid)
    __mergeSort(alist, mid + 1, end)
    #优化
    if alist[mid] > alist[mid+1]:
        merge(alist, start, mid, end)

# 合并有序数列alist[start....mid] 和 alist[mid+1...end]，使之成为有序数列
def merge(alist, start, mid, end):
    # 复制一份
    blist = alist[start:end+1]
    l = start
    k = mid + 1
    pos = start

    while pos <= end:
        if (l > mid):
            alist[pos] = blist[k-start]
            k += 1
        elif (k > end):
            alist[pos] = blist[l-start]
            l += 1
        elif blist[l-start] <= blist[k-start]:
            alist[pos] = blist[l-start]
            l += 1
        else:
            alist[pos] = blist[k-start]
            k += 1
        pos += 1

def insertionSortHelp(alist,l, r):
    for i in range(l+1,r+1):
        currentvalue=alist[i]
        position=i
        while alist[position-1]>currentvalue and position>l:
            alist[position]=alist[position-1]
            position=position-1
        alist[position]=currentvalue
    return alist
```
注意：这里进行小的优化，当数列的长度小于等于15的时候，我们一般认为数列此时基本有序，这时候采用直接插入排序非常快。

自底向上（非递归法）方法
```python
# 自底向上的归并算法
def mergeBU(alist):
    n = len(alist)
    #表示归并的大小
    size = 1
    while size <= n:
        for i in range(0, n-size, size+size):
            merge(alist, i, i+size-1, min(i+size+size-1, n-1))
        size += size
    return alist

# 合并有序数列alist[start....mid] 和 alist[mid+1...end]，使之成为有序数列
def merge(alist, start, mid, end):
    # 复制一份
    blist = alist[start:end+1]
    l = start
    k = mid + 1
    pos = start

    while pos <= end:
        if (l > mid):
            alist[pos] = blist[k-start]
            k += 1
        elif (k > end):
            alist[pos] = blist[l-start]
            l += 1
        elif blist[l-start] <= blist[k-start]:
            alist[pos] = blist[l-start]
            l += 1
        else:
            alist[pos] = blist[k-start]
            k += 1
        pos += 1
```

# 快速排序

## 思想

快速排序由 C. A. R. Hoare 在1962年提出。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序`，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

 - 从数列中挑出一个元素，称为"基准"（pivot）。
 - 重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（相同的数可以到任何一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。
 - 递归地（recursively）把小于基准值元素的子数列和大于基准值元素的子数列排序。

## 动图
![](https://pic1.zhimg.com/v2-c411339b79f92499dcb7b5f304c826f4_b.webp)


## 代码
```python
def __quickSort(alist, l, r):

    #当数列的大小比较小的时候，数列近乎有序的概率较大
    # if (r - l <= 15):
    #     insertionSortHelp(alist, l, r)
    #     return

    if l >= r:
        return
    # p = partition(alist, l, r)
    p = partitionQS(alist, l, r)

    __quickSort(alist, l, p-1)
    __quickSort(alist, p+1, r)

# 在alist[l...r]中寻找j,使得alist[l...j] <= alist[l], alist[j+1...r] >alist[l]
def partition(alist, l, r):
    pos = randint(l, r)
    alist[pos], alist[l] = alist[l], alist[pos]
    v = alist[l]
    # v = alist[l]
    j = l
    i = l + 1
    while i <= r:
        if alist[i] <= v:
            alist[j+1],alist[i] = alist[i],alist[j+1]
            j += 1
        i += 1
    alist[l], alist[j] = alist[j], alist[l]
    return j
```
快速排序一些可以优化的点

当数列近乎有序的时，由于每次选取的都是第一个数，所以造成数列分割的极其不等，此时快排蜕化成 [公式] 的算法， 此时只要随机选取基准点即可
当数列中包含大量的重复元素的时候，这一版的代码也会造成"分割不等“的问题，此时需要将重复元素均匀的分散的自数列旁
使用三路快排
