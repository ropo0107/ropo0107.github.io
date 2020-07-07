---
title: 【rl_note】 model-free Prediction And Control
categories: [学术]
tags:   [RL, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-05-29 11:51:55
---

## prediction
Evaluate the state value without knowing the MDP model, by only interacting with the environment
### MC
#### traditional
- value state of v(s) $t$ is time  that state $s_i$ is visited in an episode
    
    **Note** 在每个episode里面分为first-visit 和every-visit，代表每一条轨迹可能会重复出现状态$s$，是每一条轨迹只计算第一次出现，或者每次出现都会计算。
- $N_0 = 0;S_0(s_i) = 0; G_0 = 0$
- Increment counter  $N_t(s_i) ← N_{t-1}(s_i) + 1$
- Increment total return $S_t(s_i) ← S_{t-1}(s_i) + G_t$
- Value is estimated by mean return $v_t(s_i) = S_t(s_i)/N_t(s_i)$
- By law of large numbers(大数定律), 　$v(s_i) → v_\pi(s_i)$ as　$N(s_i) → ∞$

#### incremental
- Collect one episode $(S_1 , A_1 , R_1 , …, S_t )$
- For each state $s_i$ with computed return $G_t$
- $N_0(s_i) = 0; G_0 = 0; v_0(s_i) = 0$
- $N_t(s_i) \leftarrow N_{t-1}(s_i) + 1$
- $v_t(s_i) \leftarrow v_{t-1}(s_i) + 1/N_t(G_t -v_{t-1}(s_i)))$
  - $v_t(s_i) \leftarrow v_{t-1}(s_i) + \alpha(G_t -v_{t-1}(s_i)))$
  - $\alpha$ 也被叫做学习率， 区别于监督学习的 **learning rate**
  
## TD(0)

**Objective:** learn $v_\pi$ online from experience under policy $\pi$
- Update $v_t(s)$ toward estimated return $R + \gamma v_{t-1}(s’)$
  
    **Note** 这儿是 $R$ 是 $s\rightarrow s’$产生的奖励
- $v_t(s) \leftarrow v_{t-1}(s) + \alpha (R + \gamma v_{t-1}(s’) − v_{t-1}(s))$
- **TD target:** $R + \gamma v_{t-1}(s’)$
- **TD error:** $\delta_t = R + \gamma v_{t-1}(s’) − v_{t-1}(s)$

  - $v_\pi\doteq \mathbb{E}_\pi[G_t|S_t = s]$
  - $=\mathbb{E}_\pi[R_{t+1} + \gamma G_{t+1}|S_t = s]$
  - $=\mathbb{E}_\pi[R_{t+1} + \gamma v_\pi(S_{t+1})|S_t = s]$
  - MC将式一估计值作为目标, 之所以叫估计值， 是因为$G_t$ 的期望是未知的，需要在采样得到样本期望值
  - DP则是对式三值作为目标，之所以叫估计值， 不是因为期望的原因，会假定环境会完整的提供期望值， 这儿 $v_\pi(S_{t+1})$ 是未知的 需要用当前的 $V(S_{t+1})$
  - TD 也是估计值, 通过采样得到式三的期望 , 使用当前的估计值 $V$ 来代替真实值 $v_\pi$

**NOTE:** TD error 是一种误差， 衡量 $s$ 的估计值与更好的估计值 $R + \gamma v_{t-1}(s’)$之间的差距。 因为用到了$v_{t-1}(s’)$了， 就涉及到 **bootstrapping** 的思想，即基于前一时刻的除本身要更新的 $v_{t-1}(s)$ 外的其他值，有点动态规划的味道

### compare DP MC TD
结合上面TD error 的 Note

![](/images/posts/rl/model_free/compare_dp_mc_td.png)

![](/images/posts/rl/model_free/compare_dp_mc_td_1.png)

- MC does not bootstrap, DP TD  bootstraps
- MC TD samples, DP does not sample
- TD advantages over MC
  - Lower variance
  - Online
  - Incomplete sequences

## control
### On-policy Off-policy
- On-policy learning
  
  “Learn on the job”
  
  Learn about policy π from experience sampled from π
- Off-policy learning
  
  “Look over someone’s shoulder”
  
  Learn about policy π from experience sampled from μ

### sarsa

![](/images/posts/rl/model_free/sarsa_control.png)

![](/images/posts/rl/model_free/sarsa_al.png)

### q-learning
![](/images/posts/rl/model_free/q_learning_al.png)

### compare sarsa q-learning 
![](/images/posts/rl/model_free/diagram_sarsa_qlearning.png)

**Sarsa:** 
- On-Policy TD control
- Choose action $A_t$ from $S_t$ using policy derived from Q with $\epsilon$-greedy
- Take action $A_t$ , observe $R_{t+1}$ and $S_{t+1}$
- Choose action $A_{t+1}$ from $S_{t+1}$ using policy derived from Q with $\epsilon$-greedy
- $Q(S_t,A_t) \leftarrow Q(S_t , A_t ) + \alpha[R_{t+1} + \gamma Q(S_{t+1} , A_{t+1}) − Q(S_t , A_t)]$
- **note:** $A$ and $A’$ are sampled from the same policy so it is on-policy

**Q-Learning:**
- Off-Policy TD control
- Choose action $A_t$ from $S_t$ using policy derived from Q with $\epsilon$-greedy
- Take action $A_t$ , observe $R_{t+1}$ and $S_{t+1}$
- Then ‘imagine’ $A_{t+1}$ as $argmax Q(S_{t+1} , a' )$ in the update target 
- $Q(S_t , A_t ) \leftarrow Q(S_t , A_t ) + \alpha[R_{t+1} + \gamma \max\limits_a Q(S_{t+1} , a) − Q(S_t , A_t)]$
- **note:** In Q Learning, $A$ and $A’$ are from different policies, with $A$ being
more exploratory and $A’$ determined directly by the max operator
