---
layout: post
title:  "Linear Regression"
date:   2018-10-11
author: "Huang Qiang"
tags: [machine learning, linear regression]
comments: true
---

cost function

$$
J(\theta) = \frac{1}{2} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) ^2
$$

Update rule:

$$
\theta_j := \theta_j - \alpha \frac{\partial}{\partial \theta_j} J(\theta)
$$


$$\begin{aligned}
\frac{\partial}{\partial \theta_j}  J(\theta) &= \frac{\partial}{\partial \theta_j} \frac{1}{2} (h_\theta(x) - y)^2\\
      &= 2 · \frac{1}{2} (h_\theta(x) - y) · \frac{\partial}{\partial \theta_j} (h_\theta(x) - y)\\
      &= (h_\theta(x) - y) · \frac{\partial}{\partial \theta_j} \bigg( \sum_{i=0}^n \theta_i x_i - y \bigg) \\ 
      &= (h_\theta(x) - y) x_j
\end{aligned}$$

For a single training example

$$
\theta_j := \theta_j - \alpha(h_\theta(x^{(i)}) - y^{(i)}) x_j^{(i)}
$$


Regularization

$$
\theta_0 := \theta_0 - \frac{\alpha}{m} \sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_0^{(i)}
$$

$$
\theta_j = \theta_j - \alpha \bigg[ \bigg(\frac{1}{m}\sum_{i+1}^m (h_\theta(x^{(i)}) - y^{(i)}) x_j^{(i)} \bigg) + \frac{\lambda}{m}\theta_j \bigg]
$$