---
title:  quaternion
categories: [学术]
tags:   [Robot, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-07-11 23:34:55
---

对于坐标的变换，旋转和平移是基本的两步，平移可以是一个标量， 旋转有多种形式可以表示，（四元数， 欧拉角，旋转矩阵）
## 四元数定义
首先，定义一个你需要做的旋转。旋转轴为向量$v=(vx,vy,vz)$，旋转角度为$\theta$（右手法则的旋转）

![](/images/posts/robot/quaternion/quaternion.jpg)

图中$v=(\frac{1}{\sqrt{14}}, \frac{2}{\sqrt{14}}, \frac{3}{\sqrt{14}}), \theta = \frac{\pi}{3}$

那么与此相对应的四元数（下三行式子都是一个意思，只是不同的表达形式）
- $q=(\cos(\frac{\theta}{2}), \sin(\frac{\theta}{2})*vx,\sin(\frac{\theta}{2})*vy,\sin(\frac{\theta}{2})*vz,$
- $q=(\cos(\frac{\\pi}{6}), \sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}},sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}}, \sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}})$
- $q=(\cos(\frac{\\pi}{6}) + \sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}} i + sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}} j +  \sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}}k)$

它的共轭

- $q^{-1}=(\cos(\frac{\theta}{2}), -\sin(\frac{\theta}{2})*vx,-\sin(\frac{\theta}{2})*vy,-\sin(\frac{\theta}{2})*vz,$
- $q^{-1}=(\cos(\frac{\\pi}{6}), -\sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}}, - sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}}, -\sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}})$
- $q^{-1}=(\cos(\frac{\\pi}{6}) - \sin(\frac{\pi}{6})*\frac{1}{\sqrt{14}} i - sin(\frac{\pi}{6})*\frac{2}{\sqrt{14}} j - \sin(\frac{\pi}{6})*\frac{3}{\sqrt{14}}k)$
  
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

