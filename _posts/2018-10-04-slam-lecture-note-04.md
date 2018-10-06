---
layout: post
title: Extended Kalman Filter
author: "Cyrill Stachniss's SLAM course"

---


## Kalman Filter
 - Bayes Filter
 - Estimator for Gaussian distribution and linear (motion and sensor) models


### Kalman Filter Distribution

Everything is Gaussian.

#### Peoperties: Marginalization and Conditioning.

\[
x = (x_a, x_b)
\]

\[
p(x) = N
\]

The marginals are Gaussians

\[
p(x_a)=N \\\
p(x_b)=N
\]

The conditions are also Gaussians
\[
p(x_a|x_b) = N \\\
p(x_b|x_a) = N
\]


### Linear Models

 - Linear transition and observation model
 - Zero mean Gaussian noise

\[
x_t = A_t x_{t-1} + B_t u_t + \epsilon_t
\]

\[
z_t = C_t x_t + \delta_t
\]


\( A_t \): Matrix \( n \times n \) describes how the state evolves from \( t-1 \) to \( t \) without controls or noise.

\( B_t \): Matrix \( n \times l \) describes how the control \( u_t \) changes the state from \( t-1 \) to \( t \).

\( C_t \): Matrix \( k \times n \) describes how to map the state \( x_t \) to an observation \( z_t \).

\( \epsilon_t \), \( \delta_t \) are independent random variables representing the noise with covariance \( R_t \) and \( Q_t \) respectively.



### Linear Motion MOdel


\[
p (x_t | x_{t-1}, u_t) = \eta \exp ( - \frac{1}{2} (x_t - A_t x_{t-1} - B_t u_t)^T R_t^{-1} (x_t - A_t x_{t-1} - B_t u_t) )
\]


### Linear Observation model


\[
p(z_t | x_t) = \eta \exp( - \frac{1}{2} ( z_t - C_t x_t )^T Q_t^{-1} ( z_t - C_t x_t ) )
\]


### Kalman FIlter Algorithm


Given \( \mu_{t-1}, \Sigma_{t-1}, u_t, z_t \)

\[ 
\bar \mu_t = A_t \mu_{t-1} + B_t u_t \\\
\bar \Sigma_t = A_t \Sigma_{t-1} A_t^T + R_t \\\
K_t = \bar \Sigma_t C_t^T ( C_t \bar \Sigma_t C_t^T + Q_t)^{-1} \\\
\mu_t = \bar \mu_t + K_t (z_t - C_t \bar \mu_t ) \\\
\Sigma_t = ( I - K_t C_t ) \bar \Sigma_t
\]

Return \( \mu_t, \Sigma_t \)



Note: Production of Gaussians is intuitively weighted mean


## Extended Kalman Filter


### Non-linear Dynamic Sstems

 - Most real problems have nonlinear functions


If apply linear function on Gaussians, the result is also Gaussian. Kalman filter is built based on this.
If apply non-linear function on Gaussians, the result is not Gaussian. Kalman filter will fail.

Solution: Local linearization


### EKF Linearization : First Order Taylor Expansion

with Jacobian Matix \( G_t \) and \( H_t \)

Prediction:
\[
g(u_t, x_{t-1}) \approx g(u_t, \mu_{t-1}) + \frac{\partial g(u_t, \mu_{t-1})}{\partial x_{t-1}} (x_{t-1} - \mu_{t-1})
\]


Correction:
\[
h(x_t) \approx h(\bar{\mu}_t) + \frac{\partial h(\bar{\mu}_t)} {\partial x_t} (x_t - \bar{\mu}_t)
\]


### Linearized motion

\[
p( x_t \vert x_{t-1}, u_t ) \approx \eta \exp( - \frac{1}{2} ( x_t - g(\mu_{t01}, u_t) -G_t(x_{t-1} - \mu_{t-1}) )^T R_t^{-1}  ( x_t - g(\mu_{t01}, u_t) -G_t(x_{t-1} - \mu_{t-1}) ))
\]

where \[ ( x_t - g(\mu_{t01}, u_t) -G_t(x_{t-1} - \mu_{t-1}) ) \] is the linearized motion model,

\( R_t \) is the noise of the motion.

### Linearized observation

\[
p(z_t \vert x_t) \approx \eta \exp( - \frac{1}{2} (z_t - h(\bar \mu_t) - H_t(x_t - \bar \mu_t))^T Q_t^{-1}  (z_t - h(\bar \mu_t) - H_t(x_t - \bar \mu_t) ))
\]

where \[ (z_t - h(\bar \mu_t) - H_t(x_t - \bar \mu_t)) \] is the linearized observation model,

\( Q_t \) describes the measurement noise.

### Extended Kalman Filter Algorithm


Given \( \mu_{t-1}, \Sigma_{t-1}, u_t, z_t \)

\[ 
\bar \mu_t = g(\mu_{t-1}, u_t) \\\
\bar \Sigma_t = G_t \Sigma_{t-1} G_t^T + R_t \\\
K_t = \bar \Sigma_t H_t^T ( H_t \bar \Sigma_t H_t^T + Q_t)^{-1} \\\
\mu_t = \bar \mu_t + K_t (z_t - h_t (\bar \mu_t ) \\\
\Sigma_t = ( I - K_t H_t ) \bar \Sigma_t
\]

Return \( \mu_t, \Sigma_t \)


### KF vs. EKF
\[
A_t \Leftrightarrow K_t \\\
C_t \Leftrightarrow H_t
\]

\( K_t, H_t \) need to be re-computed at each step.


 - Works well in practice with moderate non-linearities
 - Larget uncertainty leads to increased approximation errror error
 - Complexity: \( O(k^{2.4} + n^2) \) where k is the number of parameters to estimate and n is the size of the observation vector

### Literature
 - PR Chapter 3
 - Schon and Lindsten "Manipulating the Multivariate Gaussian Density"
 - Welch and Bishop "Kalman Filter Tutorial"


### Side notes
 - Kalman filter is optimal with Gaussian distribution for linear system.
 - Correction is a weighted mean of prediction and measurement.
 - Jacobian can be seen as the tangent plane of the vector-value functions landscape.

