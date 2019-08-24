---
layout: post
title: Maximize Likelihood / Maximize Entropy duality
author: Jihong Ju

---

The Maximize Entropy and Maximize Likelihood duality states that the two problems:

 - Maximizing entropy subject to expectation constraints
 - Maximizing likelihood given parameterized constraints on the distribution


are **convex duals** of each other.


## Maximum Entropy Modeling

Unlike the wide-used Maximize Likelihood (ML) estimation, the maximum entropy estimation is less frequently seen in solving machine learning problems.

The Maximum Entropy (MaxEnt) problem is formulized as follows:


$$
\max _{\mathbf{P}} H(\mathbf{p}) \quad \text { s.t. } \sum_{i} p_{i}=1, \sum_{i} f_{ij} p_{i}=b_{j}, j=1,\dots,k
$$

where **p** is the probability simplex, H(·) is the entropy. and (**f**j, bj) an (linear) _expectation contraints_ on **p**.


To give an example of such problems, let us assume we have a biased dice and we want to estimate the probability of obtaining number 1, 2, ..., 6 each time we roll. The additional knowledge we know about the dice is that the expected number for each roll is:

$$
\sum_i^{6} i p_i = 4,
$$

as opposed to 3.5 for an unbiased dice.


According to the maximum entropy principle, we can estimate the probability of obtaining each number, **p**, by solving the maximum entropy problem:


$$
\max _{\mathbf{P}} H(\mathbf{p}) \quad \text { s.t. } \sum_{i}^6 p_{i}=1, \sum_{i}^{6} i p_{i}=4
$$

where the expected value of the obtained number is an _expectation constraint_ of this optimazation problem.  In practice, such _expectation constraints_ can usually be obtained from an empirical distribution, For instance, the expectation over the empirical distribution in this example can be obtained by rolling the dice multiple times and computing the average. 

In the next section, we will discuss how to solve the maximum entropy problem.


## Solve MaxEnt by ML estimation
With the aid of Largrange multipliers, we can derive that the solution to the MaxEnt problem is in the form of a Gibbs ditribution:

$$
q_{i}=\frac{1}{Z} \exp ^{\sum_{j=1}^{k} \mu_{j} f_{i j}}
$$

where μ1, ..., μk are the parameters and Z is the partition function.


The solution to the MaxEnt problem therefore can be found to by solving the maximize likelihood estimation given the observations **x**1, ..., **x**m:

$$

\mathop{\mathrm{argmax}} _{\mathbf{\mu}} \sum_{i=1}^{m} \log q(\mathbf{x}_{i} \vert \mathbf{\mu})

$$

As it turns out, maximizing entropy is a dual problem of maximum likelihood. The solution sets of the two problems intersect at a single point which solves both problems.


References:

1. [Amnon Shashua, Intro. to Machine Learning Lecture 3: Maximum Likelihood / Maximum Entropy Duality](http://www.cs.huji.ac.il/~shashua/papers/class3-ML-MaxEnt.pdf)
