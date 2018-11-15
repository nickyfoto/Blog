---
layout: post
title:  "Reinforcement Learning"
date:   2016-07-01
author: "Huang Qiang"
tags: [reinforcement learning]
comments: true
---

Supervised learning: $$y = f(x)$$

- 给一堆 $$(x, y)$$，目标是找到$$f$$，以便有新的 $$x$$，得出新的 $$y$$。

Unsupervised learning: $$f(x)$$

- 给一堆 $$x$$，找 $$f$$ that gives you a compact description of the set $$x$$。又被称作 clustering 或者description。

  Unsupervised learning is the machine learning task of inferring a function to describe hidden structure from unlabeled data. Since the examples given to the learner are unlabeled, there is no error or reward signal to evaluate a potential solution. 

Reinforcement learning: $$y = f(x) z$$

- 它看上去跟supervised learning比较像，但是这里给出的是 $$(x，z)$$，试着找出 $$f$$ 和 $$y$$。它是做 decision making 的一种方法。

state: $$s$$ 坐标 $$(1, 1), (4, 4)$$

model 又称作 transition model或transition function，它包含3个变量。

$$T(s, a, s')$$ (state, action, another state), 前后两个 state 可能相同。what the function produces is $$Pr(s' \mid s, a)$$

actions: things you can do in a particular state

Markov 定理的特点：
 
 - only the present matters
 - rules (Transition model) don't change

rewards: 1) R(s), 2) R(s, a), 3) R(s, a, s')

a scalar value you get for being in a state. 第一种最常见

上述 state, model, actions 和 rewards 定义了一个problem，我们需要一个solution，这个 solution 叫做 policy。

Policy is a function that takes in a state and returns an action. Any state you are in, it tells you what action you should take.

$$\Pi(s) \rightarrow a$$

$$\pi*$$ 代表 optimal policy, maximizes long-term expected reward

在前面 $$y = f(x) z$$ 中：

$$\Pi*$$ 相当于 $$f$$

$$r$$ 相当于 $$z$$

$$y$$ 相当于 $$a$$

$$x$$ 相当于 $$s$$

所以 policy 是指你在哪一个位置，应该做哪个 action。

up up right right right 被叫做 plan。

他于 policy 的区别在于，policy 是指 Whatever state I happen to be in, what's the next best thing I can do? Just always asking this question.

---

$$\Pi^* = \arg\max_{\pi} \space E[\sum_{t=0}^{\infty} \gamma^tR(S_t) \mid \Pi]$$

这只是把我们想要的写了出来，并不知道如何解。

从一个 state 的 Utility 可以写成

$$U^\pi(s) = E[\sum_{t=0}^{\infty} \gamma^tR(S_t)\mid \Pi, s_0=s]$$

$$\Pi^*(s) = \arg\max_a \sum_{s'} T(s, a, s')U(s')$$

$$U(s) \equiv U^{\pi^*}(s)$$

$$U(s)= R(s) + \gamma \max_a \sum_{s'}T(s, a, s')U(s')$$

---

Trading

- action: BUY, SELL, Do nothing
- state: HOLDING STOCK, Bollinger Value, adjusted close / SMA, P/E ration, return since entry
- reward: return from trade, daily return

### Model-based RL

Our experience tuple is

$$<s_1, a_1, s'_1, r_1>$$

$$<s_2, a_a, s'_2, r_2>$$

$$\vdots$$

where $$s'_1 = s_2$$ and so on

Build model statistically of $$T(s, a, s')$$ and $$R(s, a)$$, after we get these model we can use value iteration or policy iteration to solve the problem.

### Model-free RL (Q-learning)

What is Q?

Q is a table 

$$Q[s,a] =$$ immediate reward + discounted reward (reward for future actions)

If we have Q, how to use it to find $$\Pi$$

$$\Pi(s) = \arg\max_a (Q[s,a])$$

Find the $$a$$ such that the value of $$Q[s,a]$$ is the highest. Next question is how to build a Q table?

Bit picture

- select training data
- iterate over time $$<s, a, s', r>$$
- test policy $$\Pi$$
- repeat until converge (converge means more iteration does not make return better)

Details of iterate over time

- set start time, init $$Q[]$$
- compute $$s$$
- select $$a$$
- observe $r, s'$
- update $$Q$$

How to update Q table

$$Q'[s,a] = (1-\alpha) Q[s,a] + \alpha \cdot$$ `improved estimate`

$$\alpha$$ is the learning rate usually takes 0 to 1, our case is 0.2 .

To expand the `improved estimate` part:

$$Q'[s,a] = (1-\alpha) Q[s,a] + \alpha \cdot (r + \gamma \cdot \text{later rewards})$$

$$\gamma$$ is the discount rate between 0 to 1. A lower $$\gamma$$ means we value the future reward less.

To expand the `later reward` part:

$$Q'[s,a] = (1-\alpha) Q[s,a] + \alpha(r + \gamma \cdot Q[s', \arg\max_{a'} (Q[s',a'])]$$

Two finer points

- Success depends on exploration

Creating the state

- state is an integer
- discretize each factor
- combine

discretizing

```
stepsize = size(data) / steps
data.sort()
for i in range(0, steps)
    threshold[i] = data[(i+1) * stepsize]
```

Dyna-Q

1. Learning model T, R

	$$T'[s,a,s']$$ is a probability that in state $$s$$, take action $$a$$, what's the probability of getting to $$s'$$.

	$$R'[s,a]$$ is the expected reward, if we are in state $$s$$ and take action $$a$$.

2. Hallucinate experience

	- s = random
	- a = random
	- s' = infer from $$T[]$$
	- r = R[s,a]

3. update Q table with $$<s,a,s',r>$$

Learning T

```
init Tc[] = 0.0001
while executing, observe s,a,s'
increment Tc[s,a,s']
```

$$T[s,a,s'] = \frac{T_c[s,a,s']}{\sum_i T_c[s,a,i]}$$

Learning R

$$R'[s,a]$$ is the expected reward, if we are in state $$s$$ and take action $$a$$.

$$r$$ immediate reward

$$R'[s,a] = (1-\alpha) R[s,a] + \alpha \cdot r$$