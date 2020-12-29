---
title:  【robot】 quaternion
categories: [学术]
tags:   [Robot, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-07-11 23:34:55
---

对于坐标的变换，旋转和平移是基本的两步，平移可以是一个标量， 旋转有多种形式可以表示，（四元数， 欧拉角，旋转矩阵）

## 欧拉角
![](/images/posts/robot/quaternion/eular.gif)

图中演示的就是用 $(z, x,z^{'})$ 方法表示方向的过程。在此过程中，有如下图展示的 $(\alpha, \beta,\gamma)$三个角，分别绕着原坐标z轴(蓝)，一次旋转以后的x轴(绿)以及两次旋转以后的z轴(红)旋转，最终产生的红色坐标系即表示出目标方向。

## 四元数定义
首先，定义一个你需要做的旋转。旋转轴为向量$v=(vx,vy,vz)$，旋转角度为$\theta$（右手法则的旋转）

![](/images/posts/robot/quaternion/quaternion.jpg)

图中$v=(\frac{1}{\sqrt{14}}, \frac{2}{\sqrt{14}}, \frac{3}{\sqrt{14}}), \theta = \frac{\pi}{3}$

那么与此相对应的四元数（下三行式子都是一个意思，只是不同的表达形式）
- $$q=(\cos(\frac{\theta}{2}), \sin(\frac{\theta}{2})*vx,\sin(\frac{\theta}{2})*vy,\sin(\frac{\theta}{2})*vz$$$$$
- $$q=(\cos(\frac{\\pi}{6}), \sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}},sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}}, \sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}})$$
- $$q=(\cos(\frac{\\pi}{6}) + \sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}} i + sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}} j +  \sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}}k)$$

它的共轭

- $$q^{-1}=(\cos(\frac{\theta}{2}), -\sin(\frac{\theta}{2})*vx,-\sin(\frac{\theta}{2})*vy,-\sin(\frac{\theta}{2})*vz$$
- $$q^{-1}=(\cos(\frac{\\pi}{6}), -\sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}}, - sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}}, -\sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}})$$
- $$q^{-1}=(\cos(\frac{\\pi}{6}) - \sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}} i - sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}} j - \sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}}k)$$
  
## 四元数运算
求$w=(wx,wy,wz)$在旋转下的新的坐标$w'$
- 定义纯四元数
  $$qw=(0,wx,wy,wz) =0+ wx*i+ wy*j+wz*k$$
- 进行四元数运算
  $$qw'=q*qw*q^{-1}$$
- 产生的$qw'$一定是四元数，即第一项为0
  $$qw‘=(0,wx‘,wy’,wz‘) =0+ wx’*i+ wy‘*j+wz’*k$$
- $qw'$的后三项$(wx',wt',wz')$就是$w'$
  $$w'=(wx',wy',wx')$$

这样就完成了四元数的一次转换

## 四元数求夹角
已知两个四元数$q_0$和$q_1$, 求两个相对旋转的夹角
- 假设已知$\Delta q$:
  
    $\Delta q q_0 = q_1$

    $\Delta q q_0 q_0^{-1}= q_1 q_0^{-1}$

    $\Delta q = q_1 q_0^{-1}$
- 假设两个四元数为单位四元数 $q^{-1} = q^*$，

    $\Delta q=q_1 q_0^{×}$

- 一个单位四元数的 $t$ 次幂等同于将它的旋转角度缩放至 $t$ 倍，并且不会改变它的旋转轴

## 3D空间旋转变化量 vs 四元数4D向量空间夹角
- 四元数作为四维空间中的向量，表示为
  $$q_0=\begin{bmatrix}
  \cos(\theta_0) \\
  w_{x0}\sin(\theta_0) \\
  w_{y0}\sin(\theta_0) \\
  w_{z0}\sin(\theta_0) \\
  \end{bmatrix}， 

  q_1=\begin{bmatrix}
  \cos(\theta_1) \\
  w_{x1}\sin(\theta_1) \\
  w_{y1}\sin(\theta_1) \\
  w_{z1}\sin(\theta_1) \\
  \end{bmatrix}$$
- 根据向量点乘关系$q_0 \cdot q_1 =|q_0|\cdot |q_1|\cos(\theta)$, 假定单位四元数， 
    $$q_0 \cdot q_1 =\cos(\theta)$$
- **NOTE** 上面的东西正好为$\Delta q$ 的实部

  这也就是说，$q_0$ 与 $q_1$ 作为向量在 4D 四元数空间中的夹角 $\theta$，正好被它们旋转变化量 $∆q$ 所平分，四元数定义

  **$\Delta q$ 可作为$q_0$ 与 $q_1$ 的中间插值**


## 理解四元数
- 性质
    - 运算产生的结果也要是三维向量
    - 存在一个元运算，任何三维向量进行元运算的结果就是其本身
    - 对于任何一个运算，都存在一个逆运算，这两个运算的积是元运算
    - 运算满足结合律
- 解释
     - 四元数不是简单的映射关系，而是一个群！（后来我们知道四元数所在群为S3，而四元数所代表的三维旋转是SO(3)，前者是后者的两倍覆盖
     - 为什么公式里面是$\frac{\theta}{2}$而不是$\theta$,因为$w$不为零,结果就不与我们最初的点在同一三维空间。为了解决这个问题，所以先只旋转一半，再用逆反过来旋转另一半，这样正好将第一次旋转产生的第四维变为0，这样就能正确地映射到三维超平面。
        ![](/images/posts/robot/quaternion/three_dim.jpg)


## 参考链接:
https://zhuanlan.zhihu.com/p/87418561

https://krasjet.github.io/quaternion/quaternion.pdf

## code链接:

https://github.com/tsinghua-rll/UR5_Controller/blob/master/RTIF/LowLevel/quaternion.py