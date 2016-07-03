---
layout: post
title:  "Reinforcement Learning"
date:   2016-07-01
author: "Huang Qiang"
tags: [reinforcement learning]
comments: true
---

Supervised learning: y = f(x)

- 给一堆(x, y)，目标是找到f，以便有新的x，得出新的y。

Unsupervised learning: f(x)

- 给一堆x，找f that gives you a compact description of the set x。又被称作 clustering 或者description。

  Unsupervised learning is the machine learning task of inferring a function to describe hidden structure from unlabeled data. Since the examples given to the learner are unlabeled, there is no error or reward signal to evaluate a potential solution. 

Reinforcement learning: y = f(x) z

- 它看上去跟supervised learning比较像，但是这里给出的是（x，z），试着找出f和y。它是做decision making的一种方法。

state: s 坐标(1, 1), (4, 4)

model 又称作 transition model或transition function，它包含3个变量。

T(s, a, s') (state, action, another state), 前后两个state可能相同。what the function produces is Pr(s' \| s, a)

actions: things you can do in a particular state

Markov定理的特点：only the present matters, rules don't change

rewards: 1) R(s), 2) R(s, a), 3) R(s, a, s')

a scalar value you get for being in a state. 第一种最常见

上述 state, model, actions和 rewards定义了一个problem，我们需要一个solution，这个solution叫做policy。

Policy is a function that takes in a state and returns an action. Any state you are in, it tells you what action you should take.

π(s)→a

π* 代表 optimal policy, maximizes long-term expected reward

在前面 y = f(x) z 中：

π* 相当于 f

r 相当于 z

y 相当于 a

s 相当于 s

所以policy是指你在哪一个位置，应该做哪个action。

up up right right right 被叫做plan

他于policy的区别在于，policy是指 Whatever state I happen to be in, what's the next best thing I can do? Just always asking this question
