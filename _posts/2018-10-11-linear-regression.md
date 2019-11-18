---
layout: post
title:  "Linear Regression"
date:   2018-10-11
author: "Huang Qiang"
tags: [machine learning, linear regression]
comments: true
---

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Least Mean Square](#least-mean-square)
- [The normal equation](#the-normal-equation)
- [Evaluation Metrics](#evaluation-metrics)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Least Mean Square

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

## The normal equation

We can minimize $J$ by explicitly taking its derivatives with respect to the $\theta_j$'s, and setting them to zero.

$$\theta = (X^T X)^{-1} X^T y$$

When implementing the normal equation we want to use the `pinv` function rather than `inv` The `pinv` function will give you a value of $\theta$ even if $X^TX X$ is not invertible.

If $X^T X$ is noninvertible, the common causes might be having:

- Redundant features, where two features are very closely related (i.e. they are linearly dependent)
- Too many features (e.g. m ≤ n). In this case, delete some features or use regularization.

## Evaluation Metrics

r2 score:

$$R^2(y, \hat{y}) = 1 - \frac{\sum_{i=1}^m (y_i - \hat{y_i})^2}{\sum_{i=1}^m (y_i - \bar{y})^2}$$

Best possible score is 1.0 and it can be negative (because the model can be arbitrarily worse). A constant model that always predicts the expected value of $y$, disregarding the input features, would get a $R^2$ score of 0.0.