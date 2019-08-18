---
layout: post
title: Rao-Blackwellization in Particle Filter
author: Jihong Ju

---

### Rao-Blackwellization

>  The Rao-Blackwell theorem says that if we want an estimator with small MSE we can confine our search to estimators which are functions of the sufficient statistic.  - Richard Weber, [Statistics (Part IB) Lecture Notes](http://www.statslab.cam.ac.uk/~rrw1/stats/S03.pdf)

To give a simple example: 

If we want to approximate the joint distribution $p(a,b)$ with samples, a sampler that samples a from $p(a)$ and then determines b analytically conditioned on a, $p(b \vert a)$, is no worse than a sampler that samples directly from the joint distribution p(a, b).

The former process is sometimes called a _Rao-Blackwellization process_.

### Rao-Blackwellization for SLAM

Rao-Blackwellization provides a powerful tool for particle filter in high dimensional space. More specifically speaking, particle filter becomes inefficient when the state is high-dimensional. That is because it requires more samples to sufficiently approximate the joint distribution of all the state variables. With Rao-Blackwellization, we can factor the joint distribution to exploit dependencies between variables.

Example in robot mapping:

The joint distribution robot path $x_{0:t}$ and the landmarks $m_{1:M}$ can be represented by individual sample of the robot paths and the corresponding conditional probability of the landmarks given the robot path:

$$
\begin{aligned} p\left(x_{0 : t}, m_{1 : M} | z_{1 : t}, u_{1 : t}\right) &=\\
p\left(x_{0 : t} | z_{1 : t}, u_{1 : t}\right) & p\left(m_{1 : M} | x_{0 : t}, z_{1 : t}\right) \end{aligned}
$$

The state of the particle filter in FastSLAM becomes the robot path only $x_{1:t}$, rather than the higher dimensional counterpart, $x_{1:t}, m1, \dots, m_M$. This way the particle filtering becomes more efficient.