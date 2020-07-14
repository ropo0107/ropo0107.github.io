---
title:  【dl】 神经网络中参数量和计算量
categories: [学术]
tags:   [DL，Note]
copyright: false
reward: false
rating: false
related_posts: false
mathjax: true
date: 2020-04-24 12:51:55
---

# 参数量
![LeNet](/images/posts/dl/leNet_param.png)
## MLP
假设输入神经元数为M，输出神经元数为N，则

- bias为True时：

    则参数数量为：M*N + N（bias的数量与输出神经元数的数量是一样的）

- bias为False时：

    则参数数量为：M×N

## CNN
假设卷积核的大小为 k*k, 输入channel为M， 输出channel为N。

- bias为True时：

    则参数数量为：k×k×M×N + N（bias的数量与输出channel的数量是一样的）

- bias为False时：

    则参数数量为：k×k×M×N

- 当使用BN时，还有两个可学习的参数α和β，参数量均为N

    则参数数量为：k×k×M×N + 3×N

## LSTM
首先要说一下LSTM的结构

[参考链接](https://zhuanlan.zhihu.com/p/47907312)

- 遗忘门
    ![](/images/posts/dl/forget.webp)
- 输入们
    ![](/images/posts/dl/input.gif)
- cell 状态
    ![](/images/posts/dl/cell.webp)
- 输出门
    ![](/images/posts/dl/output.webp)

    1，把先前隐藏状态prev_ht和当前输入input合并成combine

    2，把combine输入遗忘层，决定哪些不相关数据需要被剔除

    3，用combine创建候选层，其中包含能被添加进cell状态的可能值

    4，把combine输入输入层，决定把候选层中哪些信息添加进cell状态
    
    5，更新当前cell状态
    
    6，把combine输入输出层，计算输出
    
    7，把输出和当前cell状态进行点乘，得到更新后的隐藏状态


因为input进入了四个不同的mlp，每一个可参考MLP的参数进行计算

**实际的LSTM模型**![](/images/posts/dl/lstm.jpg)


# 计算量

## MLP

假设 输入神经元数为M，输出神经元数为N，则

- 先执行M次乘法；

- 再执行M-1次加法

- 加上bias，计算出一个神经元的计算量为 （M+M-1+1）

- N个输出神经元，则总的计算量为 2M×N

## CNN

假设输入特征图（B，C，H，W），卷积核大小为K×K， 输入通道为C，输出通道为N，步长stride为S， 输出特征图大小为H2，W2.

- 一次卷积的计算量

    一个k×k的卷积，执行一次卷积操作，需要k×k次乘法操作（卷积核中每个参数都要和特征图上的元素相乘一次），k×k−1 次加法操作（将卷积结果，k×k 个数加起来）。所以，一次卷积操作需要的乘加次数：(K×K)+(K×K−1)=2×K×K−1

- 在一个特征图上需要执行卷积需要卷积的次数

    在一个特征图上需要执行的卷积次数：(（H-k+Ph）/S +1 )×(（H-k+Pw）/S +1)，Ph，Pw表示在高和宽方向填充的像素，此处假定了宽高方向滑动步长和核的宽高是一样，若不同，调整一下值即可。若不能整除，可向下取整。
    
    **Note**:卷积输出层的维度

- C个特征图上进行卷积运算的次数

    C个输入特征图上进行卷积运算的次数为C

- 输出一个特征图通道需要的加法次数

    在C个输入特征图上进行卷积之后需要将卷积的结果相加，得到一个输出特征图上卷积结果，C个相加需要C-1次加法，计算量为 ：（C-1）×H2×W2

- 输出N个特征图需要计算的次数

    N×（（C-1）×H2×W2 + （2×K×K−1）×(（H-k+Ph）/S +1 )×(（H-k+Pw）/S +1) ×C）

- 一个batch需要计算的次数

    B×N×（（C-1）×H2×W2 + （2×K×K−1）×(（H-k+Ph）/S +1 )×(（H-k+Pw）/S +1) ×C）

### LSTM
具体可参考上面LSTM流程，按照MLP的计算方式去计算


Bingo!!!
一直想写，终于把这个坑填上了
