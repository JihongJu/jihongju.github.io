---
layout: post
title: Representation - Bayesian Networks
author: Daphne Koller Probablistic Graphical Models course

---

* TOC
{:toc}

## Reasoning

A Bayesian network example

![student-grade](https://ermongroup.github.io/cs228-notes/assets/img/grade-model.png)

### Causal reasoning



### Evidential reasoning



### Inter-causal reasoning ("explaining away")



## Active trails

![4-local-structures](https://ermongroup.github.io/cs228-notes/assets/img/3node-bayesnets.png)


| Active path X-Y   | Z unobserved | Z observed |
| ----------------- | ------------ | ---------- |
| (a) cascade       | ✓            | ✕          |
| (b) cascade       | ✓            | ✕          |
| (c) common parent | ✓            | ✕          |
| (d) v-structure   | ✕            | ✓          |

A trail $X_1, \dots, X_k$ is active given $Z$ if:
 - for any v-struction $X_{i-1} \rightarrow X_i \leftarrow X_{i+1}$, that $X_i$ or one of its descendents $\in Z$
 - no other $X_j \in Z$ not at that middle position of a v-structure

## Independence and Factorization

A joint probability distribution P factor over a directed acylic graph G if it can be decomposed into a product of factors, as specified by G.

__Factorization__ of a distribution P implies independies that hold in P.

X, Y independent

$$
P(X,Y) = p(X)P(Y)
$$

$(X \perp Y \vert Z)$

$$
P(X,Y,Z) \propto \phi_1(X,Z) \phi_2(Y,Z)
$$



__d-separation__ $\text{d-sep}_{G}{(X,Y\vert Z)}$

X and Y are _d-separated_ in G given Z if there is no active trail in G between X and Y given Z.

If P factorizes over G, and $\text{d-sep}_{G}{(X,Y\vert Z)}$, then P satisfies $(X \perp Y \vert Z)$



General property: Any node is d-separated from its non-descendatns given its parents.

If P factors over G, then in P any variable is independent of its non-descendants given its parents.

__I-maps__


If P satistifies I(G), we say that G is an I-map of P.
$$
I(G) = \{(X \perp Y \mid  Z) : \text{d-sep}_{G}{(X,Y\vert Z)}\}
$$


If P factorizes over G, then G is an I-map for P.
If G is an I-map for P, then P factorizes over G.



## Dynamic Bayesian Network

Dynamic Bayesian Networks (DBN) are compact representation for encoding structured distributions over arbitrarily long temporal trajectories.


### Markov assumption

$$
P(x_{0:T}) = P(X_0) \prod_{t=0}^{T-1} P(X_{t+1} \vert X_{0:t})
$$


Assuming $ (X_{t+1} \perp X_{0:t-1} \vert X_t) $, it becomes

$$
P(x_{0:T}) = P(X_0) \prod_{t=0}^{T-1} P(X_{t+1} \vert X_{t})
$$

Could be extended to semi-markov assumption to model for example velocities of robots.

### Time invariance

Template transition model

$$
P(X_{t+1} \vert X_{t}) = P(X' \vert X)
$$

Could be extended with additional variables for abrupt factors like sensor failure, etc.


### Dynamic Bayesian Network

A dynamic Bayesian network over $X_1, \dots, X_n$ is defined by:
 - For $t = 0$, $ P(X_0) $
 - For $t > 0$, $ P(X_t \vert X_{t-1}) = P(X' \vert X) = \displaystyle \prod_{i=1}^{n} P(X' \vert \text{Parent}_{X'_i}) $


An example: Online SLAM model has unrolled (ground) model:
 - For $t = 0$, $ P(x_0) $
 - For $t > 0$, $ P(x_t, z_t \vert x_{t-1}, u_{t}, m) = P(x', z' \vert x, u, m) = P(x' \vert x, u) P(z' \vert x', m) $

![Online SLAM model is a DBN](https://www.dropbox.com/s/zcl4egxe6trgx1i/online-slam.png?dl=1)


