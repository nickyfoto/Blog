---
layout: post
title:  "Linear Regression"
date:   2018-10-11
author: "Huang Qiang"
tags: [machine learning, linear regression]
comments: true
---

Least Mean Square cost function

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

Evaluation Metrics

r2 score:

$$R^2(y, \hat{y}) = 1 - \frac{\sum_{i=1}^n (y_i - \hat{y_i})^2}{\sum_{i=1}^n(y_i - \bar{y})^2}$$

Best possible score is 1.0 and it can be negative (because the model can be arbitrarily worse). A constant model that always predicts the expected value of y, disregarding the input features, would get a $R^2$ score of 0.0.