---
title: 【rl_note】5. Policy Gradent(AC, TRPO, PPO)
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
    
    $$\nabla_\theta Ja(\theta) =\nabla_\theta \sum\limits_\tau R(\tau) P(\tau;\theta)$$
    
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

    ![](/images/posts/rl/policy_gradent/pg_ml.png)

## Reduce Variance
- temporal causality
    - 利用当前时刻后的奖励而不用整条轨迹的奖励 $G_t^i = \sum\limits_{t'=t}^{T-1}r_{t'}^i$
    - $$\nabla_\theta J(\theta) \approx \frac{1}{m} \sum\limits_{i=1}^m \sum\limits_{t=0}^{T-1} G_t^i \nabla_\theta \log \pi_\theta(a_t^i|s_t^i)$$
    - 
    - REINFORCE:
        ![](/images/posts/rl/policy_gradent/reinforence.png)
    - n-step
        $$G_t^{(1)} = r_{t+1} + \gamma v(s_{t+1})$$
        $$G_t^{(2)} = r_{t+1} + \gamma r_{t+2} + \gamma^2 v(s_{t+2})$$
        $$G_t^{(\infty)} = r_{t+1} + \gamma r_{t+2} + ... +\gamma^{T-t-1}r_T$$
- add baseline
    - 利用当前时刻后的奖励 - 平均奖励，而不用整条轨迹的奖励$G_t^i -b^i(s_t)$
    - $$\nabla_\theta J(\theta) \approx \frac{1}{m} \sum\limits_{i=1}^m \sum\limits_{t=0}^{T-1} (G_t^i-b^i(s_t)) \nabla_\theta \log \pi_\theta(a_t^i|s_t^i)$$
## PG Algorithm

![](/images/posts/rl/policy_gradent/pg_al.png)

# Problem of PG
- 训练不稳定
    - Trust region (TRPO, PPO)
    - natural policy gradient (二阶优化方法)
- 如何离线化训练 **off-policy**
    - Importance sampling（将分布转换到已知的分布）

# Advantage Actor-Critic
- 利用当前状态的**Advantage function**作为权重 ，而不用整条轨迹的奖励
    一般通过参数 $w$ 来拟合
    $$A(s_t^i, a_t^i) = Q(s_t^i, a_t^i) - V(s_t^i)$$
- $$\nabla_\theta J(\theta) \approx \frac{1}{m} \sum\limits_{i=1}^m \sum\limits_{t=0}^{T-1} A_w(s_t^i,a_t^i) \nabla_\theta \log \pi_\theta(a_t^i|s_t^i)$$
- n step
  $$\hat{A}_t^{(1)} = r_{t+1} + \gamma v(s_{t+1})- v(s_t)$$
  $$\hat{A}_t^{(2)} = r_{t+1} + \gamma r_{t+2} + \gamma^2 v(s_{t+2})- v(s_t)$$
  $$\hat{A}_t^{(\infty)} = r_{t+1} + \gamma r_{t+2} + ... +\gamma^{T-t-1}r_T- v(s_t)$$
  $\hat{A}_t^{(1)}$ low variance ,high bias
  
  $\hat{A}_t^{(\infty)}$ high variance ,low bias

# TRPO
PG + off-policy + constrain
- 步长的重要性
    - 状态和回报在改变统计特性, 优化过程中很难确定更新步长
    - policy 经常会过早陷入次忧的几乎确定的策略中
    - TRPO 通过置信域的方式保证步长为单调不减
    - ![](/images/posts/rl/policy_gradent/trust_region.png)
- 目标函数(带有折扣的奖励最大)：
    - $$\eta(\tilde{\pi}) =\mathbb{E}_{\tau|\tilde{\pi}} [\sum\limits_{t=0}^{\infty} \gamma r(s_t)] $$
    - 新策略下的期望回报 = 旧策略下的期望回报 + 新旧策略期望回报的差
        $$\eta(\tilde{\pi}) = \eta(\pi) +\mathbb{E}_{\tau|\tilde{\pi}}[\sum\limits_{t=0}^\infty \gamma^t A_\pi(s_t,a_t)]$$

        $\mathbb{E}_{\tau|\tilde{\pi}} [\sum\limits_{t=0}^\infty \gamma^t A_\pi(s_t,a_t)]$
        
        $=\mathbb{E}_{\tau|\tilde{\pi}} [\sum\limits_{t=0}^\infty \gamma^t [Q_\pi(s_t,a_t)-V_\pi(s_t)]$

        $=\mathbb{E}_{\tau|\tilde{\pi}} [\sum\limits_{t=0}^\infty \gamma^t [r(s_t)+ \gamma V_\pi(s_{t+1})-V_\pi(s_t)]$

        $=\mathbb{E}_{\tau|\tilde{\pi}} [\sum\limits_{t=0}^\infty \gamma^t r(s_t)+ \sum\limits_{t=0}^\infty (\gamma V_\pi(s_{t+1})-V_\pi(s_t))]$

        $=\mathbb{E}_{\tau|\tilde{\pi}} [\sum\limits_{t=0}^\infty \gamma^t r(s_t)] + \mathbb{E}_{s_0}[-V_\pi(s_0)]$求和展开前后相消

        $= \eta(\tilde{\pi}) - \eta(\pi)$

- 对于上式展开 

    $$\eta(\tilde{\pi}) = \eta(\pi) +\mathbb{E}_{\tau|\tilde{\pi}}[\sum\limits_{t=0}^\infty \gamma^t A_\pi(s_t,a_t)]$$

    $$\rho_\pi(s) = P(s_0=s) + \gamma P(s_1=s) + \gamma^2P(s_2=s) + ... = \sum\limits_{t=0}^\infty \gamma^t P(s_t=s|\tilde{\pi} )$$

    $$\eta(\tilde{\pi}) = \eta(\pi) +\sum_{t=0}^\infty \sum\limits_{s} P(s_t = s|\tilde{\pi})\sum\limits_a \tilde{\pi}(a|s) \gamma^t A_\pi(s,a)$$

    $$\eta(\tilde{\pi}) = \eta(\pi) +\sum\limits_{s} \rho_{\tilde{\pi}}(s) \sum\limits_a \tilde{\pi}(a|s) A_\pi(s,a)$$
    这是状态分布由新的策略$\tilde{\pi}$， 严重依赖新的状态分布

- two trick
    - 忽略状态分布的变化， 假定  
        $$\rho_{\tilde{\pi}}(s)\sim\rho_\pi(s)$$
    - 重要性采样，
        - ![](/images/posts/rl/policy_gradent/importance.png)
        - $$\sum\limits_a \tilde{\pi_\theta}(a|s) A_{\theta_{old}}(s,a) = \mathbb{E}_{a\sim\pi_{old}} [\frac{\tilde{\pi_\theta}(a|s_n)}{\pi_{\theta_{old}}(a|s_n)}A_{\theta_{old}}(s,a)]$$
  
    - 原目标：
        $$\eta(\tilde{\pi}) = \eta(\pi) +\sum\limits_{s} \rho_{\tilde{\pi}}(s) \sum\limits_a \tilde{\pi}(a|s) A_\pi(s,a)$$
    
    - 新目标：
        $$L_{\pi_{\theta_{old}}} (\tilde{\pi})= \eta(\pi) + \mathbb{E}_{s\sim \rho_{\theta {old}}, a\sim\pi_{\theta {old}}} [\frac{\tilde{\pi_\theta}(a|s_n)}{\pi_{\theta_{old}}(a|s_n)}A_{\theta_{old}}(s,a)]$$
- 梯度更新方向， 梯度优化本身就是一阶优化，
    - 在$\theta_{old}$ 处, 一阶凸优化方法中，完全一致，一阶导数一致， 
        $$\nabla_\theta \eta (\pi_{\theta_{old}}) = \nabla_\theta L_{\pi_{\theta_{old}}} (\pi_{\theta_{old}})$$ 
    - ![](/images/posts/rl/policy_gradent/one.png)

- 梯度更新步长
    - $$\eta(\tilde{\pi}) \geqslant L_\pi(\tilde{\pi}) - CD_{KL}^{MAX} (\pi, \tilde{\pi})$$

        $where , C = \frac{2\epsilon\gamma}{(1-\gamma)^2}$

    - 令 $M_{i}(\pi)  = L_{\pi_{i}}(\pi) - CD_{KL}^{MAX} (\pi_i, \pi)$
    
        $\eta (\pi_{i+1}) \geqslant M_i(\pi_{i+1})$
        
        等效于上面不等式

        $\eta (\pi_{i}) = M_i(\pi_{i})$
        
        将$\pi_i$带入不等式，两分布相同，$D_{KL}$为0

        $\eta (\pi_{i+1}) - \eta (\pi_{i}) \geqslant M_i(\pi_{i+1}) -M_i(\pi_{i})$

        即最大化$M_i$, 可保证更新步长单调不减
- Finally:
    - $\max_\theta {mize}[ L_{\pi}(\tilde{\pi}) - CD_{KL}^{MAX} (\pi, \tilde{\pi})]$
    - $\max_\theta {mize} L_{\theta_{old}}(\theta)$

        subject to : $D_{KL}^{MAX} (\theta_{old}, \theta) \leqslant \delta$

        subject to : $\bar{D}_{KL}^{\rho_{\theta_{old}}} (\theta_{old}, \theta) \leqslant \delta$

    - 线性化逼近， 二次逼近后:
        
        $\max_\theta {mize}[\nabla_\theta L_{\theta_{old}}(\theta)|_{\theta = \theta_{old}} \cdot (\theta-\theta_{old})]$gradent/ppo_clip.png)

        subject to : $\frac{1}{2} \left \| \theta- \theta_{old} \right \|^2 \leqslant \delta$

# PPO
想比于TRPO的变化就是

- TRPO 用KL作为一个约束， 而 PPO 则一快和$\theta$进行优化
- **Note:** KL 为输出$a$的距离，而不是参数$\theta$ 的距离
- $\max_\theta {mize}[ L_{\pi}(\tilde{\pi}) -\beta KL(\pi, \tilde{\pi})]$
    - If $𝐾𝐿(\theta, \theta_i) > KL_{max}$,  increase $\beta$
    - If $𝐾𝐿(\theta, \theta_i) < KL_{min}$,  decrease $\beta$
- 优化目标：
    - $$L(\theta) = \mathbb{E}_{\tau \sim \pi_{old}}[\frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{old}}(a_t|s_t)}A_{\theta_{old}}(s,a) - \beta KL(\pi_{old}, \pi)]$$

    - $$L(\theta) = \mathbb{E}_{\tau \sim \pi_{old}}[min(\frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{old}}(a_t|s_t)}A_{\theta_{old}}(s,a), clip(\frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{old}}(a_t|s_t)},1-\epsilon, 1+\epsilon)A_{\theta_{old}}(s,a))]$$

        ![](/images/posts/rl/policy_gradent/ppo_clip.png)

# DDPG

- 目的： 让DQN应用于连续的动作空间
- 