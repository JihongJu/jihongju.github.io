---
layout: post
title: Extended Information Filter
subtitle: Course Note SLAM (by Cyrill Stachniss)
author: Jihong Ju

---


# Dual Representation of Gaussians

Moments parameterization with mean $\mu$ and covariance matrix $\Sigma$

$$
p(x) = \det(2 \pi \Sigma)^{-\frac{1}{2}} \exp(-\frac{1}{2} (x-\mu)^T \Sigma^{-1} (x-\mu))
$$

Canonical parameterization with information vector $\xi$ and information matrix $\Omega$

$$
p(x) = \frac{\exp(-\frac{1}{2} \xi^T (\Omega^{-1})^T \xi)}{\det(2\pi \Omega^{-1})^{\frac{1}{2}}} \exp(-\frac{1}{2} x^T \Omega x + x^T \xi)
$$


Moments to canonical

$$
\Omega = \Sigma^{-1}
$$

$$
\xi = \Sigma^{-1} \mu
$$

Canonical to moments

$$
\Sigma = \Omega^{-1}
$$


$$
\mu = \Omega^{-1} \xi
$$

# Marginalization and conditioning

![marginalization-conditioning](https://www.dropbox.com/s/gfcxh972q9huct6/marginalization_conditioning.png?dl=1)


Marginalization is cheap for moment parameterization whereas conditioning is cheap for canonical parameterization;
Contioning is expensive for moment parameterization whereas marginalization is expensive for canonical parameterization.


# Extended Inofrmation Filter (EIF) SLAM

![eif-slam](https://www.dropbox.com/s/x931hfjf158nrwy/eif_slam.png?dl=1)

# EIF vs. EKF

 - Same expressiveness as the EKF 
 - Prediction step is more costly, Correction step is cheaper


# Literature

Extended Information Filter, Thrun et al.: “Probabilistic Robotics”, Chapter 3.5
