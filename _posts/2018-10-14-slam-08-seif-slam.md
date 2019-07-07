---
layout: post
title: Sparse Extended Information Filter
subtitle: Course Note SLAM (by Cyrill Stachniss)
author: Jihong Ju
redirect_from: "/2018/10/14/slam-lecture-note-08/"

---


# Motivation

![covmat-vs-infomat](https://www.dropbox.com/s/yalswmbiblj0z2s/cov_mat_vs_info_mat.png?dl=1)

Unlike the convariance matrix (middle), the information matrix (right) has most of its off-diganoal elements close to 0.

Information matrix itself is not sparse but can be __approximated__ with sparse matrix.

__sparse matrix__: finite number of non-zero off-diagonals, independent of the matrix size


# Graph interpretation of information matrix

Information matrix can be interpreted as MRF, where nodes denotes random variables and edges describes dependencies between variables. 

![infomat-as-mrf](https://www.dropbox.com/s/d8hqlve9ar7tttw/infomat_graph.png?dl=1)

 - $\Omega_{ij}$ tells the strength of a link between node $i$ and $j$ 
 - $\Omega_{ij}=0$ (i.e. two nodes are not connected) indicate conditional independence of the two random variables given the rest of the variables are known. They could still be dependent of each other if the other variables are unknown.


## Effect of measurement update

![measure-update](https://www.dropbox.com/s/1l45uzyl0s70woo/measurement_update.gif?dl=1)


The two landmarks $m1$, $m2$ are independent of each other given the robot pose is known.

## Effect of motion update


![motion-update](https://www.dropbox.com/s/7beq4zo44vkauky/motion_update.gif?dl=1)


# Sparsification

Sparsitification is an approximate. You lose accuracy in change of computational efficiency.

![sparsification](https://www.dropbox.com/s/839q7zh0fklcpkb/sparsification.gif?dl=1)

Keep a bank of activate landmarks and remove links between the robot pose and passive landmarks.

__Activate landmarks__: A (fixed number) subset of all landmarks, including all the currently observed one

__Passive landmarks__: All others.

The robot’s pose is linked to the active landmarks only, and the landmarks have only links to nearby landmarks (landmarks that have been active at the same time)

## Sparsification in Every Step

SEIF SLAM conducts a sparsification steps in each iteration


# Four steps of SEIF SLAM

![seif-slam](https://www.dropbox.com/s/3z0vzljxjq3j8ye/seif_slam.png?dl=1)

Now we maintain $\tilde \xi_t$, $\tilde \Omega_t$ and $\mu_t$.

The corrected mean is estimated after the measurement update of the canonical parameters $\xi_t$ and $\Omega_t$


## Motion Update

![motion-update](https://www.dropbox.com/s/qsoxtjvz7lhmhdm/seif_motion_update.png?dl=1)

(2-4) $F_x$, $\delta$, $\Delta$ are the same as those in EKF SLAM.

(5-9) Predict the information matrix

$$
\begin{split}
\bar \Omega_t & = \Sigma_t^{-1} \\
              & = [G_t \Omega_{t-1}^{-1} G_t^{-1} + R_t] ^ {-1} = [\Phi^{-1} + R_t] ^ {-1} = [\Phi^{-1} + F_x^{T} R_t^x F_x] ^ {-1} \\
              & = \Phi_t - \kappa_t \text{(Matrix Inversion Lemma)}
\end{split}
$$

Computing $\Omega_t$ is constant complexity if $\Phi_t$ is sparse.

(5-7) Computing $\Phi_t$ is also constant complexity if $\Omega_{t-1}$ is sparse.

Using $G_t^{-1} = (I + F_x^T \delta F_x)^{-1} = \dots = I + \Psi_t$,

$$
\begin{split}
\Phi_t & = [G_t \Omega_{t-1} G_t^T]^{-1} \\
       & = [G_t^T]^{-1} \Omega_{t-1} G_t^{-1} \\
       & = (I + \Psi_t) \Omega_{t-1} (I + \Psi_t^T) \\
       & = \Omega_{t-1} + \lambda_t
\end{split}
$$

where $\Psi_t$ is mapped to $(2n+3, 2n+3)$ from a $(3, 3)$ matrix by $F_x$, and $\lambda_t$ is all zeros except for a constant number of entries.


(10) Predict the information vector in constant time (the first line below is not):

$$
\begin{split}
\xi_t & = \bar \Omega_t \bar \mu_t = \bar \Omega_t (\mu_{t-1} + F_x^T \delta) \quad \text{using} \quad \mu_{t-1} = \Omega_{t-1}^{-1} \xi_{t-1} \\

      & = \bar \Omega_t \Omega_{t-1}^{-1} \xi_{t-1} + \bar \Omega_t F_x^T \delta \\
      & = \dots \\
      & = \xi_{t-1} + (\lambda_t - \kappa_t) \mu_{t-1} + \bar \Omega_t F_x^T \delta 
\end{split}
$$

where $\lambda_t$ and $\kappa_t$ are both sparse, and $F_x^T \delta$ is also sparse.

(11) Predict the mean (just the same as EKF SLAM)


## Measurement update

![seif-measurement-update-1](https://www.dropbox.com/s/ribvwzf0dor29kc/seif_measurement_update_1.png?dl=1)
![seif-measurement-update-2](https://www.dropbox.com/s/6jtu9owtxh0zoo1/seif_measurement_update_2.png?dl=1)

__Caution! $\mu_t$ on L0 and L12 are supposed to be $\bar \mu_t$__.

(1-11) Compute the expected observation $\hat z_t^i$ and the corresponding measurement Jacobian $H_t^i$ for each of the observed feature $i$, which is associated to landmark $j$. Again this step is the same as EKF SLAM.

(12-13) Compute the corrected information matrix and information vector. This step is different from EKF SLAM but the same as EIF SLAM. Note that the computation is constant for each $i$ because everything is sparse.

## Recovering the mean

Remind that the mean $\mu_t$ was needed in the previous motion update step. And it will also be needed in the sparsification step later on.

However, computing the corrected mean $\mu = \Omega^{-1} \xi$ is costly. We therefore $approximate$ the corrected mean. 

Find an approximated mean (constant dimension) that maximize the probability density function.

$$
\begin{split}
\hat \mu & = \underset{\mu}{\mathrm{argmax}} p(\mu) \\
         & = \underset{\mu}{\mathrm{argmax}} \exp(-\frac{1}{2} \mu^T \Omega \mu + \xi^T \mu)
\end{split}
$$

__To be discussed later.__


## Sparsification

Sparsification ensures a sparse information matrix as assumed by the previous steps.

