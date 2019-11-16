---
layout: post
title:  "Logistic Regression"
date:   2018-10-10
author: "Huang Qiang"
tags: [machine learning, logistic regression]
comments: true
---

$$h_\theta = g(\theta^T x) = \frac{1}{1+e^{-\theta^T x}}$$

where

$$g(z) = \frac{1}{1+e^{-z}}$$

is called **logistic function** or **sigmoid function**.

Derivative of $$g$$

$$\begin{aligned}
g'(z) &= \frac{d}{dz} \frac{1}{1+e^{-z}} \\
      &= \frac{1}{(1+e^{-z})^2}(e^{-z}) \\
      &= \frac{1}{(1+e^{-z})} · \bigg(1- \frac{1}{(1+e^{-z})} \bigg) \\
      &= g(z) (1 - g(z))
\end{aligned}$$

From probabilistic point of view we have:

$$P(y = 1 \mid x; \theta) = h_\theta(x)$$

$$P(y = 0 \mid x; \theta) = 1 - h_\theta(x)$$

Combine these two into one we have, in pmf notation

$$p(y | x; \theta) = (h_\theta(x))^y (1 - h_\theta(x))^{1-y}$$

The likelihood function of $\theta$ given training dataset $$X$$ can be written as:

$$\begin{aligned}
L(\theta) &= p(\vec{y} \mid X; \theta) \\
      &= \prod_{i=1}^m p(y^{(i)} \mid x^{(i)}; \theta) \\
      &= \prod_{i=1}^m (h_\theta(x^{(i)}))^{y^{(i)}} (1 - h_\theta(x^{(i)}))^{1-y^{i}} \\
      &= g(z) (1 - g(z))
\end{aligned}$$

Write into log likelihood:

$$\begin{aligned}
\mathcal{l}(\theta) &= \log L(\theta) \\
&= \sum_{i=1}^m y^{(i)} \log h_\theta(x^{(i)}) + (1-y^{(i)}) \log (1 - h_\theta(x^{(i)}))
\end{aligned}$$

Maximize log likelihood is equal to minimize negative log likelihood, we write the loss function $$J$$ as:

$$ J(\theta) = - \frac{1}{m} \sum_{i=1}^m [y^{(i)} \log h_\theta(x^{(i)}) + (1-y^{(i)}) \log (1 - h_\theta(x^{(i)}))]$$

In vectorized notation can be written as

$$- \frac{1}{m}(y^T · \log(h) - (1-y)^T \log(1-h)) $$

`y.shape` $$= (m, 1)$$ and $$h = h_\theta(X · \theta)$$ and `h.shape` $$= (m, 1)$$

Stochastic gradient descent rule

$$\begin{aligned}
\frac{\partial}{\partial \theta_j}\mathcal{l}(\theta) &= - \bigg(y \frac{1}{g(\theta^T x)}- (1-y)\frac{1}{1 - g(\theta^T x)} \bigg)\frac{\partial}{\partial \theta_j} g(\theta^T x) \\
&= - \bigg(y \frac{1}{g(\theta^T x)}- (1-y)\frac{1}{1 - g(\theta^T x)} \bigg)g(\theta^T x) (1 - g(\theta^Tx)) \frac{\partial}{\partial \theta_j} \theta^T x \\
&= - \big(y(1 - g(\theta^T x)) - (1-y)g(\theta^T x) \big) x_j \\
&= (h_\theta(x) - y) x_j
\end{aligned}$$

Vectorized implementation is

$$\theta := \theta - \frac{\alpha}{m} X^T (g(X · \theta) - \vec{y})$$

$$\alpha$$ is learning rate.

Add regularization term, our loss function becomes

$$ J(\theta) = - \frac{1}{m} \sum_{i=1}^m [y^{(i)} \log h_\theta(x^{(i)}) + (1-y^{(i)}) \log (1 - h_\theta(x^{(i)}))] + \frac{\lambda}{2m} \sum_{j=1}^n \theta_j^2$$

Update theta during gradient descent

$$\theta_0 = \theta_0 - \frac{\alpha}{m} \sum_{i=1}^m (h_\theta (x^{i}) - y^{(i)})x_0^{(i)}$$

$$\theta_j = \theta_j - \frac{\alpha}{m} \sum_{i=1}^m [(h_\theta (x^{i}) - y^{(i)})x_j^{(i)} + \lambda \theta_j]$$

$$j \in \{1, \ldots n\}$$

Why logistic function is a fairly natural one?


