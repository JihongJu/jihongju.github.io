---
layout: post
title: Gaussian process regression
author: Jihong Ju

---

A big advantage of Bayesian regression models over the other regression methods like logistic regression is that <u>Bayesian regression models the probability distribution of the parameter $ \theta ​$ over the entire parameter space, whereas logistic regression only finds a choice of the parameters that maximize the likelihood.</u>



In this post, we will discuss an extension of Bayesian linear regression model, **the Gaussian process regression model**.

# Multivariate Gaussian

Before we move forward, Let us quick recap some basics of Gaussian distribution. 

We denote a set of **random variables** $x \in R^n$ sampled from a Gaussian distribution with **mean values** $\mu \in R^n$ and **covariance matrix** $\Sigma \in S^{n}_{++}$ as:

$$
x \sim \mathcal{N}(\mu, \Sigma)
$$


Suppose there are two sets of variables


$$
x_A \sim \mathcal{N}(\mu_A, \Sigma_{AA}) \\
x_B \sim \mathcal{N}(\mu_B, \Sigma_{BB})
$$


The **conditional probability distribution** of $x_A$ given $x_B$ is


$$
x_A \vert x_B \sim \mathcal{N}(\mu_A + \Sigma_{AB}\Sigma_{BB}^{-1} (x_B - \mu_B),
                               \Sigma_{AA} - \Sigma_{AB}\Sigma_{BB}^{-1}\Sigma_{BA})
$$


and similarly for $ x_B \vert x_A $.

# Gaussian process

Gaussian process is an extension of Gaussian distribution. Reminds that Gaussian distribution is a distribution of **stochastic variables**. Gaussian process models a distribution of **stochastic process**, namely functions that can be defined by a collection of random variables. 

Let 


$$
\mathcal{X} = \{x_1, \dots, x_m\}
$$


be a finite collection of element $x \in R^n$ and 


$$
\{ f(x): x \in \mathcal{X} \}
$$


be a collection of random variables where 


$$
f: R^n \to R
$$


is a function that maps $x_m$ to $f(x_m)$.

There could be infinite numbers of such functions $f$. To represent a particular function, $f_0$, for example, we use a vector representation 

$$
\vec{f_0} = [f_0(x_1), \dots, f_0(x_m)]^T
$$

Any finite sub-collection of random variables sampled from a Gaussian process with mean function $ m(\cdot) ​$ and covariance function $ k(\cdot) ​$ has


$$
\begin{bmatrix}
f(x_1) \\
\vdots \\
f(x_m)
\end{bmatrix}

\sim

\mathcal{N}
\begin{pmatrix}
\begin{bmatrix}
m(x_1) \\
\vdots \\
m(x_m)
\end{bmatrix}
,
\begin{bmatrix}
k(x_1, x_1) & \cdots & k(x_1, x_m) \\
\vdots & \ddots & \vdots \\
m(x_m, x_1) & \cdots & k(x_m, x_m) \\
\end{bmatrix}
\end{pmatrix}
$$


denoted as


$$
f(\cdot) \sim \mathcal{GP}(m(\cdot),k(\cdot, \cdot))
$$

where $m(\cdot)$ can be any real-valued function, but $k(\cdot, \cdot)$ must fulfill that the resulting kernel matrix $K$ is a valid covariance matrix, i.e., $K \in S^{m}_{++}$. 

An example of such kernel is the squared exponential kernel:
$$
k_{SE}(x, x^{\prime}) = \exp(-\frac{1}{2\tau^2} \lVert x-x^{\prime} \rVert^2)
$$
This kernel has the property of local smoothness because:

- High correlation $k(x, x^{\prime}) \to 1 $ as $x$ and $x^{\prime}$ becomes close to each other, i.e., $\lVert x - x^{\prime} \rVert \to 0$
- Low correlation $k(x, x^{\prime}) \to 0 $ as $x$ and $x^{\prime}$ becomes far apart, i.e, $\lVert x - x^{\prime} \rVert \to \infty$

# Gaussian process regression

Let
$$
S = (X, \vec{y})
$$
be the training set of i.i.d. examples from an unknown distribution.

Let 
$$
T = (X_*, \vec{y_*})
$$
be the test set of i.i.d. examples from the same distribution.

We have the Gaussian process regression model


$$
y^{(i)} = f(x^{(i)}) + \epsilon^{(i)}
$$


where $\epsilon \sim \mathcal({0, \sigma^2})$ models the stochastic noise by drawing i.i.d. examples from an independent Gaussian distribution.

With a zero-mean Gaussian process prior 


$$
f(\cdot) \sim \mathcal{GP}(0, k(\cdot,\cdot))
$$


The vector representations of $f$ with the training set and with the test set satisfies


$$
\begin{bmatrix}
\vec{f} \\
\vec{f_*}
\end{bmatrix}
\vert
X, X_*
\sim
\mathcal{N}(
\vec{0},
\begin{bmatrix}
K(X, X) & K(X, X_*) \\
K(X_*, X) & K(X_*, X_*)
\end{bmatrix}
)
$$


Given our Gaussian process regression model and the i.i.d. noise assumption,


$$
\begin{bmatrix}
\vec{y} \\
\vec{y_*}
\end{bmatrix}
\vert
X, X_*
\sim
\mathcal{N}(
\vec{0},
\begin{bmatrix}
K(X, X) + \sigma^2 I & K(X, X_*) \\
K(X_*, X) & K(X_*, X_*) + \sigma^2 I
\end{bmatrix}
)
$$


Now using the conditional distribution rule for Gaussian recapped in the beginning of the post, we can compute the posterior predictive distribution analytically:


$$
\vec{y_*} \vert \vec{y}, X, X_* \sim \mathcal{N}(\mu, \Sigma)
$$


where


$$
\mu = K(X_*, X) (K(X, X) + \sigma^2 I)^{-1} \vec{y} \\
\Sigma = K(X_*,X) + \sigma^2I - K(X_*,X)(K(X,X) + \sigma^2I)^{-1}K(X,X_*)
$$


