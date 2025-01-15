---
title:  Machine Learning Base
categories: [学术]
tags:   [ML, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2021-01-10 16:51:55
---

最近懒了， 错过了好多机会， 值得庆幸的是终于知道自己想要什么，来的有点晚，
把该补的坑补上

## Liner Regression

- 定义系统:
    $$y^{*}= \omega^T x + b $$
    - $\omega\in R^n$ 为参数向量
    - b 为偏置
- 目的:最小化均方误差(MSE loss)
    - 当$y^{*(test)} = y^{(test)}$ 时误差为0， 
    - 通过MSE loss 改进权重 $\omega$
- 当导数为0时，求解正规方程:
    - $\nabla_{\omega} MSE_{train} = 0$
    - $\nabla_{\omega} {1\over m} \begin{Vmatrix} y^{*(train)}-y^{(train)} \end{Vmatrix}^2_2=0$      
    - $\nabla_{\omega}(X^{(train)}\omega -y^{(train)})^T(X^{(train)}\omega -y^{(train)})=0$  
    - $\nabla_{\omega}(\omega^T X^{(train)T}X^{(train)}\omega -2\omega^TX^{(train)T}y^{(train)}  -y^{(train)T}y^{(train)})=0$
    - 对$\omega$求导:
      - $2X^{(train)T}X^{(train)}\omega - 2X^{(train)T}y^{(train)}=0$
      - $\omega=(X^{(train)T}X^{(train)})^{-1} X^{(train)T}y^{(train)}$ 也叫正规方程

