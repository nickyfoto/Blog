---
layout: post
title:  "Naive Bayes"
date:   2018-10-09
author: "Huang Qiang"
tags: [machine learning, naive bayes]
comments: true
---

Given label $y$

$$\begin{aligned}
p(x_1, \ldots, x_{50000}|y) &= p(x_1|y)p(x_2|y,x_1)p(x_3|y,x_1,x_2) \cdots p(x_{50000}|y,x_1,\ldots,x_{49999}) \\
&= p(x_1|y)p(x_2|y)p(x_3|y) \cdots p(x_{50000}|y) \\
&= \prod_{j=1}^n p(x_j|y)
\end{aligned}$$

The first equal sign is using **multiplication rule**.

For two r.v we have 

$$p_{X,Y}(x,y) = p_Y(y) p_{X \mid Y}(x \mid y)$$

$$p_{X,Y}(x,y) = p_X(x) p_{Y \mid X}(y \mid x)$$

For more than two r.v, in event notation, we have

$$P(A \cap B \cap C) = P(A) P(B \mid A) P(C \mid A \cap B)$$

$$\begin{aligned}
    p_{X,Y,Z}(x,y,z)&= p_X(x)\ p_{Y \mid X} (y \mid x)\ p_{Z \mid X,Y} (z \mid x, y) \\
                    &= p_Y(y)\ p_{Z \mid Y} (z \mid y)\ p_{X \mid Y,Z} (x \mid y, z) \\
                    &= p_Z(z)\ p_{X \mid Z} (x \mid z)\ p_{Y \mid Z,X} (y \mid z, x)
    \end{aligned}$$

The second equal sign is based on **Naive Bayes (NB) assumption**.

Assume $$x_i$$ are **conditionally independent** given $$y$$.

What is conditionally independent?

We **define** to r.v. are **independent** if for all $$x$$, in PMF notation,

$$p_{X|A}(x) = p_X(x)$$

In event notation,

$$P(X | A) = P(X)$$

We say two event are conditionally independent 

$$p(x_{2087} \mid y) = p(x_{2087} \mid y, x_{39831})$$, this is to say $$x_{2087}$$ and $$x_{39831}$$ are conditionally independent given $$y$$.

Bayes rule tells us we can get 

$$P(A|B) = \frac{P(B|A)P(A)}{P(B)}$$

Mapping to our binary classification problem: 

$$\begin{aligned}
p(y=1|x) &= \frac{p(x|y=1)p(y=1)}{p(x)} \\
         &= \frac{\big (\prod_{j=1}^n p(x_j|y=1) \big)p(y=1)}{\big( \prod_{j=1}^n p(x_j|y=1) \big)p(y=1) + \big( \prod_{j=0}^n p(x_j|y=0) \big)p(y=0)}
\end{aligned}$$

Let

$$\phi_y = p(y=1)$$

$$\phi_{j|y=1} = p(x_j = 1|y=1)$$

$$\phi_{j|y=0} = p(x_j = 1|y=0)$$

Joint likelihood of the data:

$$\mathcal{L}(\phi_y, \phi_{j \mid y=0}, \phi_{j \mid y=1}) = \prod_{i=1}^m p(x^{(i)},y^{(i)})$$

Maximizing this with respect $$\phi_y, \phi_{j \mid y=1}, \phi_{j \mid y=0}$$ to gives the MLE (maximum likelihood estimates):

$$\phi_{j|y=1} = \frac{\sum_{i=1}^m 1\{x_j^{(i)} = 1 \wedge y^{(i)} = 1\}}{\sum_{i=1}^m1\{y^{(i)} = 1\}}$$

$$\phi_{j|y=0} = \frac{\sum_{i=1}^m 1\{x_j^{(i)} = 1 \wedge y^{(i)} = 0\}}{\sum_{i=1}^m1\{y^{(i)} = 0\}}$$

$$\phi_y = \frac{\sum_{i=1}^m 1\{y^{(i) = 1}\}}{m}$$

"$$\wedge$$" means "and". $$1\{·\}$$ is an indicator function takes on a value of 1 if its argument is true, and 0 otherwise i.e. ($$1\{\text{True}\} = 1$$, $$1\{\text{False}\} = 0$$). For example, $$1\{2 = 3\} = 0$$, and $$1\{3 = 5 − 2\} = 1$$.

When we vectorize a text into (multivariate) Bernoulli distribution, we just use the word whether it is present or not.

If the number of times a particular word occurs is important, $$x$$ becomes a [multinomial distribution](https://newonlinecourses.science.psu.edu/stat504/node/40/).

To see How to implement your own Naive Bayes in python, refer to [here](https://github.com/nickyfoto/learn/blob/master/naive_bayes.ipynb).

### References:

- [CS 229 notes2](http://cs229.stanford.edu/notes/cs229-notes2.pdf)