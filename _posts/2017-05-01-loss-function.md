---
layout: post
title:  "Loss Functions"
categories: ML
---

Loss function is on of the principle components in solving optimization problem, and optimization is one of the most important topic of machine learning. So I'd like to have a post categorized loss functions based on use cases. It is difficult to include very single loss functions being used, so I will focus on losses that I have encountered many times. This post will be continuously updating because I will find more and more losses interesting.

## Classification problem

### Hinge loss and scores

__0/1 loss__

$$ \min_\theta\sum_i L_{0/1}(\theta^Tx) $$

where $$
L_{0/1}(f) = \left\{
	\begin{array}{ll}
		1  & \mbox{if } y\cdot f \lt 0 \\
		0 & \mbox{if } y\cdot f \gt 0
	\end{array}
\right.
$$

__Hinge loss__

$$ max(0, 1 - y\cdot f) $$

### Softmax loss and probability

__Logistic loss__

$$
J(\theta) = -\left[ \sum_{i=1}^m y^{(i)} \log h_\theta(x^{(i)}) + (1-y^{(i)}) \log (1-h_\theta(x^{(i)})) \right]
$$

where $$ h_\theta(x) = \frac{1}{1+\exp(-\theta^\top x)} $$ is the __sigmoid activation function__.

__Softmax loss__

$$
J(\theta) = - \left[ \sum_{i=1}^{m} \sum_{k=0}^{1} 1\left\{y^{(i)} = k\right\} \log P(y^{(i)} = k \vert x^{(i)} ; \theta) \right]
$$

where $$P(y^{(i)} = k \vert x^{(i)}; \theta) = \frac{\exp(\theta^{(k)\top}x^{(i)})}{\sum_{j=1}^K \exp(\theta^{(j)\top} x^{(i)})}$$ is the __softmax activation function__.

## Regression problem

### Mean squared error (MSE)

$$ \min_\theta \sum_i||y-\theta^Tx||^2 $$
