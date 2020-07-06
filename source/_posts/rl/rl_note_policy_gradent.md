---
title: 【rl_note】 Policy Gradent
categories: [学术]
tags:   [RL, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-05-29 11:51:55
---

# Policy Gradent

## Value-based RL vs Policy-based RL
- value base 主要是学习一个value function 的近似，然后通过贪心的方法得到最优策略
    $$a_t=\arg \max_aQ(a,st)$$
- policy base 直接学习策略$\pi_\theta(a|s)$, 学习参数$\theta$
    - Advantages:
        - better convergence properties: we are guaranteed to converge on a local optimum (worst case) or global optimum (best case)
        - Policy gradient is more effective in high-dimensional action space
        - Policy gradient can learn stochastic policies, while value function can’t
    - Disadvantages:
        - 局部最忧
        - 方差大
    - Deterministic: given a state, the policy returns a certain action to take
    - Stochastic:
        - probability distribution of distribute actions
        - a certain Gaussian distribution for continuous action
    
## Policy Gradent for Multi-step MDPs
- 轨迹序列:
    $$\tau = (s_0, a_0, r_0, ... ,s_{T-1}, a_{T-1},r_{T-1},s_T, r_T) \sim (\pi_\theta, P(s_{t+1} | s_t, a_t))$$

- 轨迹概率:

    $$P(\tau;\theta) = \mu(s_0) \prod\limits_{t=0}^{T-1}\pi_\theta(a_t|s_t)p(s_{t+1}|s_t, a_t)$$

    $\pi_\theta(a_t|s_t)$ 仅该项和 Policy 有关

    $p(s_{t+1}|s_t, a_t)$ 其为动力学模型， 仅与环境有关

- 累计回报:
    $$R(\tau) = \sum\limits_{t=0}^T r_t$$
- 优化目标:
    $$J(\theta) = \sum\limits_\tau R(\tau) P(\tau;\theta)$$
    对轨迹$\tau$进行累加
- Goal
  - goal:
    $$\theta^* = \arg\max\limits_\theta J(\theta) = \arg\max\limits_\theta \sum\limits_\tau R(\tau) P(\tau;\theta)$$
  
  - gradient:
    
    $$\nabla_\theta J(\theta) =\nabla_\theta \sum\limits_\tau R(\tau) P(\tau;\theta)$$
    
    $$=\sum\limits_\tau R(\tau)  P(\tau;\theta) \nabla_\theta logP(\tau;\theta)$$
  - Monte carlo:
    $$\nabla_\theta J(\theta) \approx \frac{1}{m} \sum\limits_{i=1}^m R(\tau_i) \nabla_\theta logP(\tau_i ;\theta)$$
    
    采样后轨迹概率 $P(\tau;\theta)$ 变为采样的加和
    
  - 分解 $\nabla_\theta logP(\tau_i ;\theta)$:
  
    $$\nabla_\theta logP(\tau_i ;\theta) = \nabla_\theta log[\mu(s_0) \prod\limits_{t=0}^{T-1}\pi_\theta(a_t|s_t)p(s_{t+1}|s_t, a_t)]$$
    $$=\nabla_\theta[log(\mu(s_0)) + \sum\limits_{t=0}^{T-1} (\log \pi_\theta(a_t|s_t) + \log p(s_{t+1}|s_t, a_t)))]$$
    $$=\sum\limits_{t=0}^{T-1} \nabla_\theta \log \pi_\theta(a_t|s_t)$$
    
    $\theta$仅与policy有关  
  - Finaly:
    $$\nabla_\theta J(\theta) \approx \frac{1}{m} \sum\limits_{i=1}^m [R(\tau_i) \sum\limits_{t=0}^{T-1} \nabla_\theta \log \pi_\theta(a_t^i|s_t^i)]$$

    $\nabla_\theta \log\pi_\theta(a_t^i|s_t^i)$作为**score function**代表梯度更新方向

    $R(\tau_i)$表示更新方向上的权重， 偏向于好的轨迹

## Comparison to Maximum Likelihood
- PG
    $$\nabla_\theta J(\theta) \approx \frac{1}{M} \sum\limits_{m=1}^M (\sum\limits_{t=1}^{T} \nabla_\theta \log \pi_\theta(a_t^m|s_t^m)) (\sum\limits_{t=1}^{T}r(s_t^m, a_t^m))$$
- Maximum Likelihood
    $$\nabla_\theta J_{ML}(\theta) \approx \frac{1}{M} \sum\limits_{m=1}^M (\sum\limits_{t=1}^{T} \nabla_\theta \log \pi_\theta(a_t^m|s_t^m))$$
- 往更好的轨迹偏移

    ![](/images/posts/rl/policy_gradent/pg_al.png)

## Reduce Variance
- temporal causality
    - 利用当前时刻后的奖励而不用整条轨迹的奖励 $G_t^i = \sum\limits_{t'=t}^{T-1}r_{t'}^i$
    - $$\nabla_\theta J(\theta) \approx \frac{1}{m} \sum\limits_{i=1}^m \sum\limits_{t=0}^{T-1} G_t^i \nabla_\theta \log \pi_\theta(a_t^i|s_t^i)$$
    - 
    - REINFORCE:
        ![](/images/posts/rl/policy_gradent/reinforence.png)
- add baseline
    - 利用当前时刻后的奖励 - 平均奖励，而不用整条轨迹的奖励$G_t^i -b^i(s_t)$
    - $$\nabla_\theta J(\theta) \approx \frac{1}{m} \sum\limits_{i=1}^m \sum\limits_{t=0}^{T-1} (G_t^i-b^i(s_t)) \nabla_\theta \log \pi_\theta(a_t^i|s_t^i)$$
