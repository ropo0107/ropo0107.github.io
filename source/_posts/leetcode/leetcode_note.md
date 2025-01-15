---
title:  LeetCode的笔记
categories: [杂记]
tags:   [leetcode, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-03-04 02:51:55
---

# 菜鸡刷leetcode
用来记录一些好的点子

## 面试题57 - II. 和为s的连续正数序列
 输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

示例：
```
输入：target = 9
输出：[[2,3,4],[4,5]]
示例 2：

输入：target = 15
输出：[[1,2,3,4,5],[4,5,6],[7,8]]
```

### 解法1
 -  遍历

### 解法2
 - 双指针 ij
 - 可能最长一定是1开始,
 - 当小于target 右指针sum = sum + j , j++
 - 当大于target 左指针sum = sum - i , i++
 - 当等于target 输出

### 解法3
 - x 起点 y 终点 
 - 2(x+y)∗(y−x+1)=target
 - y^2+y−x^2−x−2∗target=0 解方程组利用求根
 - 判别式 b^2-4acb 开根需要为整数
 - 最后的求根公式的分母需要为偶数，因为分子为 2

### 解法4
 - 最长的为1开始的,即前n项的和>=target
 - 这样最对项数i为n*(n+1)/2 =target 最大i为 sqrt(2*target)
 - 若k为起点 target = k*i + i(i-1)/2
 - 判断k为整数即可


