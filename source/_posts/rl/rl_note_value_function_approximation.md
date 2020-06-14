---
title: 【rl_note】 Value Function Approximation
categories: [学术]
tags:   [RL, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-05-29 11:51:55
---

## Challenges with large MDPs:
- too many states or actions to store in memory
- too slow to learn the value of each state individually

## Solution
- 为了避免通过查表的方式表示Value function, state-action function，Policy
- Estimate with function approximation
    - $\hat{v} (s, \mathbf{\omega}) \approx  v^π (s)$
    - $\hat{q}(s, a, \mathbf{\omega}) \approx  q^π (s, a)$
    - $\hat{\pi}(a, s, \mathbf{\omega}) \approx  π(a \mid s)$

- Linear VFA often works well given the right set of features, But it requires manual designing of the feature set
- Alternative is to use a much richer function approximator that is able to directly learn from states without requiring the feature design
- Nonlinear function approximator: Deep neural networks


## Value Function Approximation with an Oracle
- We assume that we have the oracle for knowing the true value for
$v^π (s)$ for any given state $s$
- Then the objective is to find the best approximate representation of $v^π (s)$
- Thus use the **mean squared error** and define the loss function as
$$J(\omega) = \mathbb{E}_π [(v^π (s) − \hat{v}(s, w))^2]$$
- Follow the gradient descend to find a local minimum
$$\Delta \omega = − {1 \over 2} \alpha ∇_\omega J(\omega)$$
$$\omega_{t+1} = \omega_t + \Delta \omega$$

## Linear Value Function Approximation

- Represent value function by a linear combination of features, s is feature vector
$$\hat{v}(s,\omega) = x(s)^T \omega = \sum_{j=1}^n x_j(s)\omega_j$$
- The objective function is quadratic in parameter $\omega$
$$J(\omega) = \mathbb{E}_\pi[v^\pi(s) − x(s)^T \omega]^2$$
- Thus the update rule is as simple as
$$\Delta\omega = \alpha(\mathbf{v^\pi(s)} − \hat{v}(s, \omega))x(s)$$
$$Update = StepSize × PredictionError × FeatureValue$$
- Stochastic gradient descent converges to global optimum. Because in the linear case, there is only one optimum, thus local optimum is automatically converge to or near the global optimum.
- Special case
    - Table lookup is a special case of linear value function approximation
    - Table lookup feature is one-hot vector **(只有一个元素为1)** as follows

        $$x^{table}(s) = [0(s = s_1 ), ..., 1(s = s_n )]^T$$

    - Then we can see that each element on the parameter vector $w$ indicates the value of each individual state

        $$\hat{v}(s, \omega) = [0(s = s_1 ), ..., 1(s = s_n)] [w_1 , ..., w_n]^T$$

    - Thus we have（对于的权重就是对应k的权重） 
        $$\hat{v}(s_k , \omega) = \omega_k$$


## No Oracle
- For MC, the target is the actual return $G_t$

    $$\Delta\omega = \alpha (\mathbf{G_t} − \hat{v}(s_t , \omega) ∇\omega \hat{v}(s_t , \omega)$$

    **$G_t$** 因为是采样，所以这儿是无偏的估计， 但是会有噪声
- For TD(0), the target is the TD target $R_{t+1} + \gamma \hat{v}(s_{t+1} , \mathbf{\omega})$
    $$\Delta\omega = \alpha(\mathbf{R_{t+1} +\gamma \hat{v} (s_{t+1} , \omega)} − \hat{v}(s_t , \omega) ∇ \omega\hat{v} (s_t , \omega)$$
    **TD target** 有偏估计， 因为用到了旧时刻的 $\omega$

Generalized policy iteration
![](/images/posts/rl/value_function_approximation/policy_iteration.png)

### 同理 $v(s)$ 可替换为 $q(s,a)$ 
- MC
    $$\Delta\omega = \alpha (\mathbf{G_t} − \hat{q}(s_t ,a_t, \omega) ∇\omega \hat{q}(s_t ,a_t, \omega)$$

- sarsa
    $$\Delta\omega = \alpha(\mathbf{R_{t+1} +\gamma \hat{q} (s_{t+1} ,a_{t+1}, \omega)} − \hat{q}(s_t,a_t,\omega) ∇ \omega\hat{q} (s_t, a_t, \omega)$$

    **Note:** 训练困难
        
    - 在优化过程中，即近似 **Bellman backup** 又近似 **value function**, 
    - off-policy control: **behavior policy** and **target policy** 

- q-learning
    $$\Delta\omega = \alpha(\mathbf{R_{t+1} +\gamma \max_a \hat{q}(s_{t+1} ,a, \omega)} − \hat{q}(s_t,a_t,\omega) ∇ \omega\hat{q} (s_t, a_t, \omega)$$

![](/images/posts/rl/value_function_approximation/sarsa_semi_gradient.png)

### Deadly Triad
- Function approximation: 利用函数估计会有误差
- Bootstrapping: 主要影响 TD 和 MP
- Off-policy training:  **behavior policy** and **target policy** 
  
## Convergence
![](/images/posts/rl/value_function_approximation/convergence.png)

___

![](/images/posts/rl/value_function_approximation/convergence_al.png)


## DQN
