---
title: 【rl_note】 Markov Decision Processes
categories: [学术]
tags:   [RL, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-05-29 11:51:55
---

## overview
- Markov Processes
- Markov Reward Processes(MRPs)
- Markov Decision Processes (MDPs)
- v(s)，q(s,a) transform
- policy iteration
- value iteration
  
## Markov
### MP (S,P)
The future is independent of the past given the present

- history state $h_t = {s_1,s_2,s_3...,s_t}$
- $P(s_{t+1}\mid s_t) = P(s_{t+1}\mid h_t)$
- $P(s_{t+1}\mid s_t, a_t) = P(s_{t+1}\mid h_t, a_t)$
- State transition matrix P specifies 
    $$p(s_{t+1} = s' |s_t = s)$$

### MRP (S,P,R, $\gamma$)
- Markov Chain + reward
- Definition of Markov Reward Process (MRP)
    - $S$ is a (finite) set of states ($s ∈ S$)
    - P is dynamics/transition model that specifies 
    $$P(S_{t+1} = s' |s_t = s)$$
    - R is a reward function 
    $$R(s_t = s) = E[r_t \mid s_t = s]$$
    - Discount factor $\gamma \in [0,1]$
- Value Function
   - $$G_t = R_{t+1}+\gamma R_{t+2}+ ... = \sum\gamma^kR_{t+k+1}$$

        $$V_\pi(s) = E_\pi[G_t\mid S_t=s]$$
    - **Bellman equation**
  
        $$V_\pi(s) = R_{t+1} + \gamma\sum P(s'\mid s)V(s')$$
    - $$ V = R + \gamma P V$$
        $$ V = (1-\gamma P)^{-1}R$$
        **Note**Matrix inverse takes the complexity $O(N^3 )$ for N states


### MDP (S,P,A,R, $\gamma$)
- MRP + decisions $A$.
- Definaation
    - $S$ is a finite set of states
    - $A$ is a finite set of actions
    - $P^a$ is dynamics/transition model for each action
        $$P(s_{t+1} = s' |s_t = s, a_t = a)
    - 
    - $R$ is a reward function 
        $$R(s_t = s, a_t = a) = E[r_t \mid s_t = s, a_t = a]
    - 
    - Discount factor $\gamma \in [0, 1]$
- Policy in MDP
    - $\pi(a|s) = P(a_t = a|s_t = s)$
    - $P_\pi(s'|s) = \sum\limits_{a \in A}\pi(a|s)P(s‘|s, a) 　　　a\in A$  

    - $R(s) = \sum\limits_{a \in A} (a|s)R(s,a)$
- Value Function
    - state value function
        
        用来衡量当前状态的价值
        $$V^\pi = E_\pi[G_t \mid s_t = s]$$

    - action value function
        
        用来衡量状态s下a的价值
        $$q^\pi(s,a) = E_\pi[G_t \mid s_t =s, a_t = a]$$
    
    - $v^\pi(s) \And q^\pi(s,a)$
        $$ v^\pi(s) = \sum\limits_{a\in A} \pi(a | s)q^\pi(s,a) $$

    - 状态s 和 动作 a 之间的转换
        ![](/images/posts/rl/mdp/compare_mp_mdp.png)
        ![](/images/posts/rl/mdp/one.png)
        ![](/images/posts/rl/mdp/two.png)
        
## v(s),q(s,a)
**NOTE:** MRP 对应 V(s), MDP 对应 Q(s,a)

## Dynamic Programming
已知MDP过程，**转移矩阵** 和 **奖励函数** 都是已知的
### Policy Iteration
Bellman Expectation Equation
![](/images/posts/rl/mdp/policy_iteration.png)
#### policy evaluation
iteration on the Bellman expectation backup
$$V_i(s) = \sum\limits_{a \in A} \pi(a|s)(R(a,s) + \sum\limits_{s' \in S}P(s'|s,a)v_{i-1}(s')) $$

#### policy improvement
greedy on action-value function q

$$q_{\pi_i}(s,a) = R(s,a)+\gamma\sum\limits_{s\in S}P(s'|s,a) V_{\pi_i}(s')$$

$$\pi_{i+1}(s) =  arg \max\limits_a q_{\pi_i}(s,a)$$

### Value Iteration
Bellman Optimality Equation

整个过程没有策略的引入

#### iteration on the Bellman optimality backup
$$v_{i+1}(s) \larr \max\limits_{a\in A}R(s,a) + \gamma\sum\limits_{s'\in S}P(s'|s,a)v_i(s') $$

#### retrieve the optimal policy after the value iteration
在前面迭代收敛后再，根据 $v_{end}(s)$ 选取策略

$$\pi^*(s) \larr \argmax\limits_a R(s,a) + \gamma\sum\limits_{s'\in S}P(s'|s,a)v_{end}(s') $$

