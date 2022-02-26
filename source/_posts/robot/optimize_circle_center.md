---
title:  【robot】优化空间中点的圆心
categories: [学术]
tags:   [Robot]
copyright: false
reward: false
rating: false
related_posts: false
date: 2021-11-16 15:30:00
---

# 目的

在空间中取得一堆点集， 已知它们在空间中是一条弧线，通过观测到的点的坐标，找到点集的圆心。

# 方法

- 点集在空间中是共面的。
- 圆心过圆上任意两点的公垂线
## 平面方程
假设平面方程为：
$$ax+by+cz=1$$
则：
$$M= \begin{bmatrix}
x_1 & y_1 & z_1 \\
x_2 & y_2 & z_2 \\
\vdots & \vdots  & \vdots \\
x_n & y_n  & z_n
\end{bmatrix}$$

$$A = \begin{bmatrix}
a  \\
b  \\
c  \\
\end{bmatrix}$$

$$L_1 = \begin{bmatrix}
1  \\
1  \\
1  \\
\end{bmatrix}$$

$$MA=1$$

由最小二乘得到：

$$A=(M^TM)^{-1}M^TL_1$$

## 求圆心

假定圆心$C = (x_c, y_c, z_c)$, 任意两点是$P_1=(x_1, y_1, z_1)$、$P_2=(x_2, y_2, z_2)$。则：

$$(x_2-x_1,y_2-y_1,z_2-z_1)(\frac{x_1+x_2}{2}-x_c,\frac{y_1+y_2}{2}-y_c, \frac{z_1+z_2}{2}-z_c) = 0$$

$$\Rightarrow \Delta x_{12} \cdot x_c + \Delta y_{12} \cdot y_c + \Delta z_{12} \cdot z_c -L_2 = 0$$

$$\begin{bmatrix}
x_{12} & y_{12} & z_{12} \\
x_{13} & y_{13} & z_{13} \\
\vdots & \vdots  & \vdots \\
x_{(n-1)n} & y_{(n-1)n}  & z_{(n-1)n}
\end{bmatrix}
\begin{bmatrix}
x_c  \\
y_c  \\
z_c  \\
\end{bmatrix}=
\begin{bmatrix}
l_{12}  \\
l_{13}  \\
\vdots   \\
l_{(n-1)n}  \\
\end{bmatrix}
$$
其中
$$l_{(n-1)n} = \frac{x_n^2+y_n^2+z_n^2-x_{n-1}^2-y_{n-1}^2-z_{n-1}^2} {2}$$

即构建$A^TC=1$约束下求$BC=L_2$

$$f(c) = \left \| BC-L_2 \right \|^2 +\lambda(A^TC-1)$$

其中，$\lambda$ 为拉格朗日乘子。对关于 $C$ 与 $\lambda$ 求导，并令导数值为0。

$$
\begin{bmatrix}
B^TB & A \\
A^T & 0 \\
\end{bmatrix}
\begin{bmatrix}
C \\
\lambda \\
\end{bmatrix} = 
\begin{bmatrix}
B^TL_2 \\
1 \\
\end{bmatrix}
$$

$$
\begin{bmatrix}
C \\
\lambda \\
\end{bmatrix}=
\begin{bmatrix}
B^TB & A \\
A^T & 0 \\
\end{bmatrix}^{-1}
\begin{bmatrix}
B^TL_2 \\
1 \\
\end{bmatrix}
$$


## 参考链接

https://zhuanlan.zhihu.com/p/154517678