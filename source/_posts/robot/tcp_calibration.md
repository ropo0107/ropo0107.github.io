---
title:  【robot】相机标定机器人TCP
categories: [学术]
tags:   [Robot]
copyright: false
reward: false
rating: false
related_posts: false
date: 2021-11-16 17:30:00
---

# 目的
标定机器人末端的TCP

# 标定过程

## 标定中的坐标系变换
建立几个坐标系：
- $B$ 机器人Base坐标系
- $E$ 机器人末端法兰盘坐标系
- $C$ 相机坐标系
- $t$ TCP空间点的位置

满足：

$$
\begin{bmatrix}
^C_tP \\
1 \\
\end{bmatrix}=
^C_BT \cdot ^B_ET
\begin{bmatrix}
^E_tP \\
1 \\
\end{bmatrix}
$$

$$
\begin{bmatrix}
^C_tP \\
1 \\
\end{bmatrix}=
\begin{bmatrix}
^C_BR  & ^C_BT \\
0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
^B_ER  & ^B_ET \\
0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
^E_tP \\
1 \\
\end{bmatrix} = 
\begin{bmatrix}
^C_BR \cdot ^B_ER \cdot ^E_tP + ^C_BR \cdot ^B_ET + ^C_BT \\
1 \\
\end{bmatrix}
$$

$$^C_tP = ^C_BR \cdot ^B_ER \cdot ^E_tP + ^C_BR \cdot ^B_ET + ^C_BT$$

其中 $^C_tP、 ^B_ER 、^B_ET$已知

## 做平移运动， 保$^B_ER$不变

$$\left\{\begin{matrix}
^C_tP_1 = ^C_BR \cdot ^B_ER \cdot ^E_tP + ^C_BR \cdot ^B_ET_1 + ^C_BT \\
\\
^C_tP_2 = ^C_BR \cdot ^B_ER \cdot ^E_tP + ^C_BR \cdot ^B_ET_2 + ^C_BT 
\end{matrix}\right.$$

可得：
$$^C_tP_1 - ^C_tP_2 =^C_BR \cdot (^B_ET_1 - ^B_ET_2) $$

求得机器人相对于相机的旋转$^C_BR$

## 使末端姿态发生变化

$$\left\{\begin{matrix}

^C_tP_4 = ^C_BR \cdot ^B_ER_4 \cdot ^E_tP + ^C_BR \cdot ^B_ET_4 + ^C_BT \\
\\
^C_tP_5 = ^C_BR \cdot ^B_ER_5 \cdot ^E_tP + ^C_BR \cdot ^B_ET_5 + ^C_BT 
\end{matrix}\right.$$

做差可得：

$$^C_tP_4 - ^C_tP_5 =^C_BR \cdot (^B_ER_4 - ^B_ER_5) \cdot ^E_tP+ ^C_BR \cdot (^B_ET_4 - ^B_ET_5) $$

满足 $AX=B$ 可用最小二乘法解得$^E_tP$

其中：
$$A = ^C_BR \cdot (^B_ER_4 - ^B_ER_5)$$
$$B = (^C_tP_4 - ^C_tP_5) - ^C_BR \cdot (^B_ET_4 - ^B_ET_5)$$
$$X = (A^TA)^{-1}A^TB$$

<!-- 
## TCF的计算

- 已知TCP，即可求出$^B_tP$, 
- 假定相机坐标系和机器人坐标系没有相对变化, 即$^C_BT=E$，$^B_tP=^C_tP$
- 根据点云配准求得$^C_{t_{tcp}}P = T ^C_{t_{cam}}P$, 即将相机下点和机器人坐标系下点进行配准。
- 将$^C_ET$点集和$^C_tT$，求得$^E_tT$, 拿到R

- Tip
    - 假定相机坐标系和机器人坐标系没有相对变化，相等于在算两个点云$T$中包含了$^C_BT$ 
-->

## 方法 AX=XB
无论眼在手上,还是固定姿态的相机,均可转化为求解 $AX=XB$ 方程的形式

### 代数方法

### 非线性优化


## 参考链接:
https://aipiano.github.io/2019/09/07/%E6%9C%BA%E6%A2%B0%E8%87%82%E4%B8%8E%E7%9B%B8%E6%9C%BA%E7%9A%84%E6%A0%87%E5%AE%9A/


