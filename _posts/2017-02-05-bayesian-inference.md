---
layout: post
title:  "Bayesian inference"
author: "Jihong"
---

### Bayes Rule

Before we talk about Bayesian inference, let us first quickly recap the Bayes rule. The Bayes Rule describes the probability of a hypothesis based on the given evidence. It is often written in a format with conditional probability as follows:

$$p(H|E) = \frac{p(E|H)p(H)}{p(E)}$$

where

- $$H$$ is any _hypothesis_ whose probability might be affected by the evidence $$E$$
- $$E$$ is the _evidence_, i.e. data observed, that might affect the probability of hypothesis $$H$$
- $$p(H)$$ is the estimation of the probability of the hypothesis $$H$$ **before** the evidence $$E$$ is observed, a.k.a. the _prior probability_
- $$p(H \vert E)$$ is the estimation of the probability of the hypothesis $$H$$ **after** the evidence $$E$$ is observed, a.k.a. the _posterior probability_
- $$p(E \vert H)$$ is the probability of observing the evidence $$E$$ given the hypothesis $$H$$
- $$p(E)$$ the __marginal likelihood__ which is the same for all the hypothesis.


### Bayesian inference

Suppose a number of examples $${x_1, x_2, ..., x_n} \in X$$ are observed, the goal is to find a parametrized density function $$f(x \vert \theta)$$ which best fit to the distribution of $$X$$. It is equivalent to infer the unknown parameters $$\theta$$ based on the observation. Using Bayes rule, the posterior proability for $$\theta$$ given $$X$$ is given by:

$$p(\theta|X) = \frac{p(X|\theta) p(\theta)}{p(X)}$$

$$p(X \vert \theta)$$, the likelihood of observing $$X$$ given model parameters, is given by the model function $$f(x \vert \theta)$$. The choice of the model function can be seperated from the estimation of model parameters. That is Bayesian inference can be applied regardless of which model to be used.

$$p(\theta)$$ is a prior about the model parameters, which is what we think in advance the parameters would be. If we have enough data, it turns out the influence of the prior is rather insignificant. Sometimes we simply use the uniform distribution when we don't care about the prior. Alternatively, one might want to specify the prior in terms of a parametrized distribution to include prior knowledge when necessary.

$$p(X)$$ is a normalizing term which is generally intractable. Fortunately, this term is a constant, independent to $$\theta$$. Therefore, we often do not care much about it while estimating the model parameters and the previous equation becomes:

$$p(\theta|X) \propto p(X|\theta) p(\theta)$$

Once a likelihood function is specified and the corresponding prior distribution is chosen, the most probable model parameters can be estimated by maximizing the posterior distribution, a.k.a. maximum a posteriori estimation (MAP). In case the prior is uniform, miximizing the posterior is equivalent to miximizing the likelihood, a.k.a. maximum-likelihood estimation (MLE).

In practice, [conjugate prior](https://en.wikipedia.org/wiki/Conjugate_prior) of the likelihood function, for which the posterior distributions are in the same family as the prior, is preferred. It allows to explicitly compute the posterior (with the normalization term) because the posterior given by a closed-form expression.


### Expectation-maximization (EM) algorithm

In practice, most often the likelihood function consists of not only the unknown parameters $$\theta$$ but also some latent variables $$Z$$. The marginal likelihood of the observed data is:

$$p(X|\theta) =\sum_{Z} p(X,Z|\theta)$$

The number of computations in this equation grows exponentially along with the number of latent variables. Therefore it is intractable to explicitly maximize the marginal likelihood distribution and find the most probable parameters $$\theta$$.

However, the EM algorithm provides a method to find the most probable parameters by iteratively running the following two steps:

- Expectation step (E-step): Estimate the posterior distribution of Z given $$X$$ and $$\theta$$ at step $$t$$

  $$Q^{(t)}(Z):=p(Z|X,\theta^{(t)})$$

- Maximization step (M-step): Maximize the expected value of the log likelihood function given the posterior distribution

  $$\theta^{(t+1)} := \arg\max Q^{(t)}(Z) \log \frac{p(X,Z|\theta^{(t)})}{Q^{(t)}(Z)}$$

Check [this lecture note by Andrew Ng](http://cs229.stanford.edu/notes/cs229-notes8.pdf) out for the prove of convergence.


### Posterior approximation

With complex statistic models, the posterior distribution $$p(Z \vert X)$$ given $$X$$ is too complicated to work with. The complication arises from the dependencies among the latent parameters $$Z$$, resulting in the intractable marginal distribution of some variables. This issue generally occurs to many Bayesian inference problems and is referred as intractable coupling.

The solution of this problem is to find the approximations of those intractable posterior distributions and work with the approximation.

There are mainly two categories of methods for finding the approximations: __Markov chain Monte Carlo (MCMC)__ methods or __Variational inference__ methods.



### Markov chain Monte Carlo (MCMC)

MCMC generates the numeric approximation of the intractable posterior distribution by sampling. It follows the general idea of [Monte Carlo method](https://en.wikipedia.org/wiki/Monte_Carlo_method): using repeated random sampling to obtain numerical results which simulates the solution of problems we cannot solve analytically. The underlying logic is we can compute any statistic of a posterior distribution as long as we have N simulated samples from that distribution ([See details](http://www.mit.edu/~ilkery/papers/GibbsSampling.pdf)).

Markov chain (the first MC in MCMC) refers that the sampling is based on constructing a [Markov chain](https://en.wikipedia.org/wiki/Markov_chain) which takes the posterior distribution as the target equilibrium distribution.

The most commonly used sampling algorithms are [Metropolis–Hastings algorithm](https://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm)
and [Gibbs sampling](https://en.wikipedia.org/wiki/Gibbs_sampling).

#### Metropolis sampler

The principle ingredients of Metropolis sampler are:

 - a method propose a random jump for the next step
 - a criteria determines the acceptance of the proposals

The acceptance criteria ensures that all the regions in the space are reachable and regions of high posterior probability is relatively more often visited than those of low posterior probability.


#### Gibbs sampler

Gibbs sampler iteratively samples from a joint distribution based on conditional distributions. It updates a single parameter at a time by sampling from its conditional distribution when the other parameters are fixed.

Consider a joint distribution over all the variables, including the observed variables, the unknown parameters and the latent variables, $$p(X,Z)$$,  the samples of this joint distribution can be drawn by


> Initialize $$Z^{(0)}$$ \\
> __for__ iteration $$t = 1,...,n$$ __do__ \\
> &nbsp;&nbsp;&nbsp; __for__ $$i = 1,...,k$$ __do__ \\
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sampling $$Z_i^{(t)} \sim p(Z_i=Z_i^{(t)}\vert X, Z_1^{(t)},...,Z_{i-1}^{(t)}, Z_{i+1}^{(t-1)}, ...,Z_k^{(t-1)})$$

Though samples from this process represent the joint distribution, the marginal distribution over the a subset of parameters given observed data $$X$$ is simply approximated by considering only the samples for parameters of interest and ignoring the rest.


Check [this post by Thomas Wiecki](http://twiecki.github.io/blog/2015/11/10/mcmc-sampling/) out for more about Metropolis sampler and [this post ](http://cs.brown.edu/research/ai/dynamics/tutorial/Documents/GibbsSampling.html) for Gibbs sampling.

In general, the more samples are taken, the more accurate the approximation is.
However, it is difficult to determine how many samples is "accurate enough". The number of sampling iterations is usually based on "rule of thumb". The downside of MCMC includes it is not deterministic and it can be slow to achieve an "accurate enough" approximation. The downsides of MCMC becomes one of the motivations of using variational inference.

### Variational inference

The variational inference approximates the posterior over a set of latent variables $$p(Z \vert X)$$ with a variational distribution, $$Q(Z)$$. The choice of variational distribution is usually restricted to a family of distributions of simpler form than $$P(Z \vert X)$$.

The inference is performed by minimizing the dissimilarity between $$Q(Z)$$ and $$P(Z \vert X)$$. The most common dissimilarity measure for the two distributions is the [Kullback–Leibler divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence) (KL-divergence):

$$D_{KL}{(Q\|P)} = \sum_{Z} Q(Z) \log \frac{Q(Z)}{P(Z \vert X)} =  \log P(X) - L(Q)$$

where $$\log P(X)$$ is constant with respect to $$Q$$ so that minimizing the KL-divergence is equivalent to maximize $$L(Q)$$.

$$L(Q)$$ is called the (negative) variational free energy given by:

$$L(Q) = - \sum_{Z} Q(Z) \log \frac{Q(Z)}{P(Z, X)}$$


With an appropriate choice of $$Q$$, as we mentioned in the beginning of this session, it becomes possible to compute and maximize $$L(Q)$$. Finding the optimal variational distribution then becomes an optimization problem with respect to $$L(Q)$$

<!-- In practice, the variational distribution is often factorized into multiple partitions of the latent variables:

$$Q(Z)=\prod_{i=1}^{M}q_i(Z_i \vert X)$$

and each partition has the form:

$$\ln q_{j}^{\ast} (Z_j \vert X) = E_{i \neq j}[\ln p(Z,X)] + const.$$

where $$E_{i\neq j}[\ln p(Z, X)]$$ is the expectation of the logarithm of the joint probability of the data and latent variables, taken over all variables not in the partition and $$const.$$ is a normalization term which has no effect to the optimization.

Each of the above expression is dependent to the expectations of latent variables not in the current partition. This naturally suggests an iterative EM-like algorithm: for each partition -->


Check [this post by Jason Eisner](https://www.cs.jhu.edu/~jason/tutorials/variational.html) out for the  optimization techniques e.g. __Gradient Ascent__, __variational EM__.
