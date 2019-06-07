---
layout: post
title: Extended Kalman Filter
subtitle: Course Note SLAM (by Cyrill Stachniss)
author: Jihong Ju

---

* TOC
{:toc}


## Bayes Filter

SLAM is a state estimation problem, it estimates the map and the robot pose. Bayes filter is a tool for state estimation.

Recap: Bayes filter is a recursive filter with
 - Prediction step

$$
\bar{bel}(x_t) = \int p(x_t \vert x_{t-1}, u_t) bel(x_{t-1}) dx_{t-1}
$$

 - Correction step

$$
bel(x_t) = p(z_t \vert x_t) \bar{bel}(x_t)
$$


## Kalman Filter

 - Bayes Filter
 - Estimator for the linear Gaussian case
 - Optimal solution for linear models and Gaussian distributions

### Distribution and properties

Everything is Gaussian:

$$
p(x) = \det(2 \pi \Sigma)^{-\frac{1}{2}} \exp(-\frac{1}{2} (x-\mu)^T \Sigma^{-1} (x-\mu))
$$

Properties: Marginalization and Conditioning

Given

$$
p(x_a, x_b) = N(\mu, \Sigma)
$$

The marginals are Gaussians

$$
p(x_a)=N(\mu_a, \Sigma_{aa}) \\
p(x_b)=N(\mu_b, \Sigma_{bb})
$$

The conditions are also Gaussians

$$
p(x_a|x_b) = N(\mu_a + \Sigma_{ab} \Sigma_{bb}^{-1}(b-\mu_b), \Sigma_{aa} - \Sigma_{ab}\Sigma{bb}^{-1}\Sigma_{ba}) \\
p(x_b|x_a) = N(\mu_b + \Sigma_{ba} \Sigma_{aa}^{-1}(a-\mu_a), \Sigma_{bb} - \Sigma_{ba}\Sigma{aa}^{-1}\Sigma_{ab})
$$


### Linear Models

 - Linear transition and observation model
 - Zero mean Gaussian noise

$$
x_t = A_t x_{t-1} + B_t u_t + \epsilon_t
$$

$$
z_t = C_t x_t + \delta_t
$$


$ A_t $: Matrix $ (n \times n) $ describes how the state evolves from $ t-1 $ to $ t $ without controls or noise.

$ B_t $: Matrix $ (n \times l) $ describes how the control $ u_t $ changes the state from $ t-1 $ to $ t $.

$ C_t $: Matrix $ (k \times n) $ describes how to map the state $ x_t $ to an observation $ z_t $.

$ \epsilon_t $, $ \delta_t $ are independent random variables representing the noise with zero mean and covariance $ R_t $ and $ Q_t $ respectively.



### Linear Motion Model


$$
p (x_t | x_{t-1}, u_t) = \det(2 \pi R_t)^{-\frac{1}{2}} \exp ( - \frac{1}{2} (x_t - A_t x_{t-1} - B_t u_t)^T R_t^{-1} (x_t - A_t x_{t-1} - B_t u_t) )
$$


### Linear Observation model


$$
p(z_t | x_t) = \det(2 \pi Q_t)^{-\frac{1}{2}} \exp( - \frac{1}{2} ( z_t - C_t x_t )^T Q_t^{-1} ( z_t - C_t x_t ) )
$$


### Everything stays Gaussian

Given an initial Gaussian belief, the belief is always Gaussian 

 - Prediction step

$$
\bar{bel}(x_t) = \int p(x_t \vert x_{t-1}, u_t) bel(x_{t-1}) dx_{t-1}
$$

 - Correction step

$$
bel(x_t) = \eta p(z_t \vert x_t) \bar{bel}(x_t)
$$

Only linear operations involved. Apply linear operations to a Gaussian Process results in a Gaussian Process.

(Proof see Probabilistic Robotics, Sec. 3.2.4)


## Kalman Filter Algorithm

Given $ \mu_{t-1}, \Sigma_{t-1}, u_t, z_t $

Prediction step

$$
\bar \mu_t = A_t \mu_{t-1} + B_t u_t \\
\bar \Sigma_t = A_t \Sigma_{t-1} A_t^T + R_t
$$

Correction step

$$
K_t = \bar \Sigma_t C_t^T ( C_t \bar \Sigma_t C_t^T + Q_t)^{-1} \\
\mu_t = \bar \mu_t + K_t (z_t - C_t \bar \mu_t ) \\
\Sigma_t = ( I - K_t C_t ) \bar \Sigma_t
$$

Return $ \mu_t, \Sigma_t $


 - Production of Gaussians is intuitively weighted mean, the correction is thus essentially a weighted mean of prediction and measurement. The weights are dependent of the measurement noise $Q_t$
 - If $Q_t \rightarrow \infty$, then Kalman gain $K_t \rightarrow 0$, $\mu_t \rightarrow \bar{\mu_t}$, $\Sigma_t \rightarrow \bar{\Sigma_t}$, No correction to the prediction based on the measurement.
 - If $Q_t \rightarrow 0$, then Kalman gain $K_t \rightarrow C_t^{-1}$, $\mu_t \rightarrow C_t^{-1} z_t$, $\Sigma_t \rightarrow 0$, The measurement overrides prediction completely.


## Extended Kalman Filter

Extend the Kalman Filter with first order taylor expansion.


### Non-linear Dynamic Systems

Most real problems have nonlinear functions

$$
x_t = g(x_{t-1}, u_t) + \epsilon_t
$$

$$
z_t = h(x_t) + \delta_t
$$

![linear-func](https://www.dropbox.com/s/0cg5igor2r9zvpx/linear_func.png?dl=1) | ![nonlinear-func](https://www.dropbox.com/s/b9s0u69lygnnnji/non_linar_func.png?dl=1)


 - If apply linear function on Gaussians, the result is also Gaussian. Kalman filter is built based on this.
 - If apply non-linear function on Gaussians, the result is not Gaussian. Kalman filter is not applicable anymore.

To resolve this: Local linearization.


### EKF Linearization

With first-order Taylor expension,

Prediction:

$$
g(u_t, x_{t-1}) \approx g(u_t, \mu_{t-1}) + \frac{\partial g(u_t, \mu_{t-1})}{\partial x_{t-1}} (x_{t-1} - \mu_{t-1})
$$

We denote the Jacobian Matix as $ G_t $.

Correction:

$$
h(x_t) \approx h(\bar{\mu}_t) + \frac{\partial h(\bar{\mu}_t)} {\partial x_t} (x_t - \bar{\mu}_t)
$$

We denote the Jacobian Matix as $ H_t $.

Remind that Jacobian can be seen as the tangent plane of the vector-value functions landscape.

![linearized-function](https://www.dropbox.com/s/psuigxclm3h6pcw/linearized_func.png?dl=1)


### Linearized motion

$$
p( x_t \vert x_{t-1}, u_t ) \approx \det(2 \pi R_t)^{-\frac{1}{2}} \exp( - \frac{1}{2} ( x_t - g(\mu_{t01}, u_t) -G_t(x_{t-1} - \mu_{t-1}) )^T R_t^{-1}  ( x_t - g(\mu_{t01}, u_t) -G_t(x_{t-1} - \mu_{t-1}) ))
$$

where $$ ( x_t - g(\mu_{t01}, u_t) -G_t(x_{t-1} - \mu_{t-1}) ) $$ is the linearized motion model,

$ R_t $ is the noise of the motion.

### Linearized observation

$$
p(z_t \vert x_t) \approx \det(2 \pi Q_t)^{-\frac{1}{2}} \exp( - \frac{1}{2} (z_t - h(\bar \mu_t) - H_t(x_t - \bar \mu_t))^T Q_t^{-1}  (z_t - h(\bar \mu_t) - H_t(x_t - \bar \mu_t) ))
$$

where $$ (z_t - h(\bar \mu_t) - H_t(x_t - \bar \mu_t)) $$ is the linearized observation model,

$ Q_t $ describes the measurement noise.


### Extended Kalman Filter Algorithm

Given $ \mu_{t-1}, \Sigma_{t-1}, u_t, z_t $

Prediction step:

$$
\bar \mu_t = g(\mu_{t-1}, u_t) \\
\bar \Sigma_t = G_t \Sigma_{t-1} G_t^T + R_t \\
$$

Correction step:

$$
K_t = \bar \Sigma_t H_t^T ( H_t \bar \Sigma_t H_t^T + Q_t)^{-1} \\
\mu_t = \bar \mu_t + K_t (z_t - h_t (\bar \mu_t ) \\
\Sigma_t = ( I - K_t H_t ) \bar \Sigma_t \\
$$

Return $ \mu_t, \Sigma_t $

### KF vs. EKF

$$
A_t \Leftrightarrow K_t \\
C_t \Leftrightarrow H_t
$$

$ K_t, H_t $ need to be re-computed at each step.


 - Works well in practice with moderate non-linearities
 - Larget uncertainty leads to increased approximation errror error
 - Complexity: $ O(k^{2.4} + n^2) $ where k is the number of parameters to estimate and n is the size of the observation vector

### Literature
 - PR Chapter 3
 - Schon and Lindsten "Manipulating the Multivariate Gaussian Density"
 - Welch and Bishop "Kalman Filter Tutorial"

