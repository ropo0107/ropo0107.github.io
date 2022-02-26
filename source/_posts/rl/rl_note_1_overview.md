---
title:  【rl_note】1. Overview
categories: [学术]
tags:   [RL, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-05-29 11:51:55
---


## Difference between Reinforcement Learning and Supervised Learning

- Sequential data as input (not i.i.d)

- The learner is not told which actions to take, but instead must discover which actions yield the most reward by trying them.
  
- Trial-and-error exploration (balance between exploration and

- There is no supervisor, only a reward signal, which is also delayed


## Features of Reinforcement Learning

- Trial-and-error exploration
- Delayed reward
- Time matters (sequential data, non i.i.d data)
- Agent’s actions affect the subsequent data it receives (agent’s action changes the environment)


## Introduction to Sequential Decision Making

### Rewards

- A reward is a scalar feedback signal
- Indicate how well agent is doing at step t
- Reinforcement Learning is based on the maximization of rewards:
All goals of the agent can be described by the maximization of expected
cumulative reward.

### Sequential Decision Making

- Objective of the agent: select a series of actions to maximize total
    future rewards
- Actions may have long term consequences
- Reward may be delayed
- Trade-off between immediate reward and long-term reward


### Sequential Decision Making

- The history is the sequence of observations, actions, rewards.
  $$H_t = O_1, R_1, A_1, ..., A_{t-1}, O_t, R_t$$

- What happens next depends on the history
- State is the function used to determine what happens next
$$ S_t = f(H_t)$$


### Sequential Decision Making

- Environment state and agent state:
  $$S^e_t = f^e(H_t)$$
  $$S^a_t = f^a(H_t)$$

- **Full observability** : agent directly observes the environment state
  $$O_t = S^e_t = S^a_t$$

- **Partial observability** : agent indirectly observes the environment, formally as partially observable Markov decision process (POMDP)
  $$O_t \neq ^e_t \neq S^a_t$$
  
  

## Major Components of an RL Agent

- **Policy**:

  ​	agent’s behavior function

- **Value function**: 

  ​	how good is each state or action

- **Model**:

  ​	agent’s state representation of the environment


### Policy

- A policy is the agent’s behavior model

- It is a map function from state/observation to action.

- Stochastic policy: Probabilistic sample
  $$\pi(a \mid s) = P[A_t = a\mid S_t = s]$$

- Deterministic policy:

  $$a^* = \arg\max\limits_a \pi(a\mid s)$$

### Value function

- Value function: expected discounted sum of future rewards under a
    particular policy $\pi$
    
- Discount factor weights immediate vs future rewards

- Used to quantify goodness/badness of **states**

    $$G_t = R_{t+1}+\gamma R_{t+2}+ ... = \sum\limits_{k=0}
    ^\infty\gamma^kR_{t+k+1}$$
    **$G_t$is the total step discounted reward from time step $t$.**
    $$V_\pi(s) = \mathbb{E}_\pi[G_t\mid S_t=s]$$

    $$ = \mathbb{E}_\pi[\sum\limits^\infty_{k=0}\gamma^kR_{t+k+1}\mid S_t=s]$$

- Q-function at state $s$ and action $a$

    $$Q_\pi(s,a) = \mathbb{E}_\pi[G_t\mid S_t=s, A_t = a] = \mathbb{E}_\pi[\sum\limits^\infty_{k=0}\gamma^kR_{t+k+1}\mid S_t=s, A_t=a]$$


### Model

A model predicts what the environment will do next

- Predict the next state:(转移概率)
  $$P^a_{ss'} = P[S_{t+1}=s' \mid S_t=s, A_t=a]$$

- Predict the next reward:

  $$R^a_s = P[R_{t+1} \mid S_t=s, A_t=a]$$




## Types of RL Agents based on What the Agent Learns

- Value-based agent:
    - Explicit: Value function
    - Implicit: Policy (can derive a policy from value function)
- Policy-based agent:
    - Explicit: policy
    - No value function
- Actor-Critic agent:
    - Explicit: policy and value function


## Types of RL Agents on if there is model

- Model-based
    - Explicit: model
    - May or may not have policy and/or value function
- Model-free
    - Explicit: value function and/or policy function
    - No model.

## Two Fundamental Problems in Sequential Decision Making

- Planning
    - Given model about how the environment works.
    - Compute how to act to maximize expected reward without external interaction.

- Reinforcement learning
    - Agent doesn’t know how world works
    - Interacts with world to implicitly learn how world works
    - Agent improves policy (also involves planning)


## Exploration and Exploitation

- Agent only experiences what happens for the actions it tries!
- How should an RL agent balance its actions?
    - Exploration: trying new things that might enable the agent to make better
       decisions in the future
    - Exploitation: choosing actions that are expected to yield good reward given
       the past experience
- Often there may be an exploration-exploitation trade-off
    - May have to sacrifice reward in order to explore & learn about potentially
       better policy


