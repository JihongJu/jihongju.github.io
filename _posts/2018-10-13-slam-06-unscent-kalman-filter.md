---
layout: post
title: Unscent Kalman Filter
subtitle: Course Note SLAM (by Cyrill Stachniss)
author: Jihong Ju
redirect_from: "/2018/10/13/slam-lecture-note-06/"

---

Extended Kalman Filter extends Kalman Filters by linearizing the non-linear models with first-order Taylor expansion.

Unscent Kalman Filter instead extends Kalman Filters with Unscent transformation.


## Unscent transformation

- Compute a set of (so-called) sigma points
- Transform each sigma point through the non-linear function
- Compute Gaussian from the transformed and weighted sigma points
- Compute a Gaussian from weighted points


Sigma Points
Q: How to choose the sigma points & How to set the weights?
A: Choose the sigma points $X$ and the corresponding weights $W$ so that the chosen points and weights fit exactly the orignial Gaussian distribution:

$$
\sum_{i} w_i x_i = 1
$$

$$
\mu = \sum_{i} w_i x_i
$$

$$
\Sigma = \sum_{i} w_i (x_i - \mu_i) (x_i - \mu_i)^T
$$


(No unique solution for $X$, $W$)
