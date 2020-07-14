---
title:  【dl】 GAN_公式推导
categories: [学术]
tags:   [RL, Note,GAN]
copyright: false
reward: false
rating: false
related_posts: false
mathjax: true
date: 2020-03-24 12:51:55
---

# GAN
## GAN数学描述
**目标:** 以假乱真的生成器
- 高水平的鉴赏家
- 高水平的工艺大师

原始数据概率分布: 

$$P_{data} =(x;\theta)$$

生成器概率分布:  
$$P_z =(z;\theta_g)$$

这儿 $\theta_g$ 表示概率的参数，

生成器网络表示：

$$x=G (z;\theta_g)$$

这儿 $\theta_g$ 表示网络的参数，这儿不对生成器进行具体建模，用一个神经网络(**确定性函数**)去描述 

鉴别器网络表示：**为伯努利分布**

$$D (x;\theta_d)$$

$x$ 可以来着 $P_{data}$ 或者 生成器 $G$ 



**高水平专家**

- if $x$ from $P_{data}$ ，Then $D(x)$  $\Uparrow$
- if $x$ from $P_g$ ，Then $D(x)$ $\Downarrow$
  -  $D(x)$ 下降，即，$1-D(G(z))$ $\Uparrow$

为计算方便，可加上log

- if $x$ from $P_{data}$ ，Then $logD(x)$ $\Uparrow$
- if $x$ from $P_g$ ，Then $log(1-D(G(z)))$ $\Uparrow$
  
目标：

$$\max_D E_{x\sim{P_{data}}}[\log(D(x))]+E_{z\sim{P_{z}}}[log(1-D(G(z)))]$$

**高水平大师**

- if $x$ from $P_g$ ，Then $D(G(z))$ $\Uparrow$

即

- if $x$ from $P_g$ ，Then $log(1-D(G(z)))$ $\Downarrow$

目标：

$$\min_G E_{z\sim{P_{z}}}[log(1-D(G(z)))]$$


总目标：

$$\min_G \max_D E_{x\sim{P_{data}}}[\log(D(x))]+E_{z\sim{P_{z}}}[log(1-D(G(z)))]$$

## GAN全局最优解


$\min_G \max_D E_{x\sim{P_{data}}}[\log(D(x))]+E_{z\sim{P_{z}}}[log(1-D(G(z)))]$

恒等

$V(D,G)=\min_G \max_D E_{x\sim{P_{data}}}[\log(D(x))]+E_{x\sim{P_{g}}}[log(1-D(x)]$

for fix $G$ , $\max_D V(D,G)$

$\max_D V(D,G)=\int{P_{data}\log{D}}dx+ \int{P_g\log(1-D)}dx$

$=\int{[P_{data}\log{D}+ P_g\log(1-D)]}dx$

$=\int{[P_{data}\log{D}+ P_g\log(1-D)]}dx$

对 $D$ 求偏导

$
\frac {\partial max_D V(D,G)} {\partial D } = \int \frac {\partial} {\partial D } [P_{data}\log{D}+ P_g\log(1-D)]dx
$

$=\int[ P_{data}{1 \over D}+P_g{-1 \over 1-D}]dx$

令导数为0：


$D^*_G = {P_{data} \over P_{data}+P_g}$

故 $D$ 存在最大值

将 $D^*_G$ 带入 $V(D,G)$

$min_G max_D V(D,G)$

$=min_G V(D^*_G,G)$

$=\min_G E_{x\sim{P_{data}}}[\log{P_{data}\over P_{data}+P_g}]+E_{x\sim{P_{g}}}[log({P_{g}\over P_{data}+P_g})]$

$=\min_G E_{x\sim{P_{data}}}[\log{1\over2}{P_{data}\over {P_{data}+P_g\over 2} }]+E_{x\sim{P_{g}}}[log({1\over2}{P_{g}\over {P_{data}+P_g\over2}})]$

$=\min_G KL(P_{data} \parallel {P_{data}+P_g\over2})+KL(P_{g} \parallel {P_{data}+P_g\over2})-log4$

$\geqslant-log4$

当且仅当：$p_{data}=P_g={P_{data}+P_g\over2}$ 时成立。

**最优解：**


$P^*_g = P_{data}$


$D^*={1\over2}$

最终判别器在概率角度已经不能够分辨真假。

----
- $P_{data}+P_g$ 这儿不是概率分布，凑出 $P_{data}+P_g \over 2$ 为概率分布。


KL公式：

分子分母必须保证为概率分布

$$KL(p \parallel q) =E_p log{p\over q}$$

打公式可太费劲了

---

