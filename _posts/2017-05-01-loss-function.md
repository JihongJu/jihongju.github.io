---
layout: post
title: Objective and loss functions
author: Jihong

---

## 0/1 loss

$$
J(\theta) = \sum_i^m L_{0/1}(\theta^Tx)
$$

where

$$
L_{0/1}(f) = \left\{
	\begin{array}{ll}
		1  & \mbox{if } y\cdot f \lt 0 \\
		0 & \mbox{if } y\cdot f \gt 0
	\end{array}
\right.
$$


## Hinge loss

$$ J(\theta) = max(0, 1 - y\cdot f) $$


## Logistic loss


$$
J(\theta) = -\left[ \sum_{i=1}^m y^{(i)} \log h_\theta(x^{(i)}) + (1-y^{(i)}) \log (1-h_\theta(x^{(i)})) \right]
$$


where $ h_\theta(x) = \frac{1}{1+\exp(-\theta^\top x)} $ is the __sigmoid activation function__.

## Cross entropy
$$
J(\theta) = - \left[ \sum_{i=1}^{m} \sum_{k=0}^{1} 1\left\{y^{(i)} = k\right\} \log P(y^{(i)} = k \vert x^{(i)} ; \theta) \right]
$$
where $P(y^{(i)} = k \vert x^{(i)}; \theta) = \frac{\exp(\theta^{(k)\top}x^{(i)})}{\sum_{j=1}^K \exp(\theta^{(j)\top} x^{(i)})}$ is the __softmax activation function__.

### Mean squared error (MSE)

$$  \sum_i||y-\theta^Tx||^2 $$
