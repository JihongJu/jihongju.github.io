---
layout: post
title: EKF SLAM
author: Cyrill Stachniss SLAM course

---

* TOC 
{:toc}

# SLAM

(S)multaneanously building a (m)ap of the world (a)nd (l)ocalizing the robot in the map

## Definition

Given the robot controls $u_{1:T}$ and observations $z_{1:T}$, return a map of the world $m$ and the robot paths $x_{0:T}$ 

## Three Main Paradigms
#TODO ref to the other posts
 - Kalman filter
 - Particle filter
 - Graph-based


## Bayes Filter ([Recap](https://jihongju.github.io/2018/10/02/slam-lecture-note-03/))

Recursive filter for state estimation with 

 - Prediction step

$$
\bar{bel}(x_t) = \int p(x_t \vert x_{t-1}, u_t) bel(x_{t-1}) dx_{t-1}
$$

 - Correction step

$$
bel(x_t) = p(z_t \vert x_t) \bar{bel}(x_t)
$$


# EKF for Online SLAM

Apply EKF on SLAM to estimate the robot pose and the landmarks location in the environment.


## Online SLAM (Recap)

$$
p(x_t, m \vert z_{1:t}, u_{1:t})
$$


![Graphical model of online SLAM](https://www.dropbox.com/s/zcl4egxe6trgx1i/online-slam.png?dl=1)


## EKF Algorithm ([Recap](https://jihongju.github.io/2018/10/04/slam-lecture-note-04/#extended-kalman-filter-algorithm))

![ekf-slam](https://www.dropbox.com/s/9h5ahlmewpxbr62/ekf_slam.png?dl=1)


## State representation

Assuming known correspondences, the state space (robot pose and n landmarks in 2D space) is

$$
x_t = (x, y, \theta, m_{1,x}, m_{1,y}, \dots, m_{n, x}, m_{n, y})
$$


Probablistic representation (assuming Gaussian distribution)

$$
\mu = 
\begin{pmatrix}
x \\
y \\
\theta \\
m_{1,x} \\
m_{1,y} \\
\vdots \\
m_{n,x} \\
m_{n,y} \\
\end{pmatrix}
$$


$$
\Sigma = 
\begin{pmatrix}
\sigma_{xx} & \sigma_{xy} & \sigma_{x\theta} & | & \sigma_{x m_{1,x}} & \sigma_{x m_{1,y}} & \ldots & \sigma_{x m_{n,y}} & \sigma_{x m_{n,y}} \\
\sigma_{yx} & \sigma_{yy} & \sigma_{y\theta} & | & \sigma_{y m_{1,x}} & \sigma_{y m_{1,y}} & \ldots & \sigma_{y m_{n,y}} & \sigma_{y m_{n,y}} \\
\sigma_{\theta x} & \sigma_{\theta y} & \sigma_{\theta \theta} & | & \sigma_{\theta m_{1,x}} & \sigma_{\theta m_{1,y}} & \ldots & \sigma_{\theta m_{n,y}} & \sigma_{\theta m_{n,y}} \\
- & - & - & | & - & - & - & - & - \\
\sigma_{m_{1,x}x} & \sigma_{m_{1,x}y} & \sigma_{m_{1,x}\theta} & | & \sigma_{m_{1,x} m_{1,x}} & \sigma_{m_{1,x} m_{1,y}} & \ldots & \sigma_{m_{1,x} m_{n,y}} & \sigma_{m_{1,x} m_{n,y}} \\
\sigma_{m_{1,y}x} & \sigma_{m_{1,y}y} & \sigma_{m_{1,y}\theta} & | & \sigma_{m_{1,y} m_{1,x}} & \sigma_{m_{1,y} m_{1,y}} & \ldots & \sigma_{m_{1,y} m_{n,y}} & \sigma_{m_{1,y} m_{n,y}} \\
\vdots  & \vdots & \vdots & |  & \vdots & \vdots & \ddots & \vdots & \vdots\\
\sigma_{m_{n,x}x} & \sigma_{m_{n,x}y} & \sigma_{m_{n,x}\theta} & | & \sigma_{m_{n,x} m_{1,x}} & \sigma_{m_{n,x} m_{1,y}} & \ldots & \sigma_{m_{n,x} m_{n,y}} & \sigma_{m_{n,x} m_{n,y}} \\
\sigma_{m_{n,y}x} & \sigma_{m_{n,y}y} & \sigma_{m_{n,y}\theta} & | & \sigma_{m_{n,y} m_{1,x}} & \sigma_{m_{n,y} m_{1,y}} & \ldots & \sigma_{m_{n,y} m_{n,y}} & \sigma_{m_{n,y} m_{n,y}} \\
\end{pmatrix}
$$

In simpler form
$$
\mu = 
\begin{pmatrix}
x \\
m \\
\end{pmatrix}
$$,
and 
$$
\Sigma = 
\begin{pmatrix}
\Sigma_{xx} & \Sigma_{xm} \\
\Sigma_{mx} & \Sigma_{mm} \\
\end{pmatrix}
$$

# EKF SLAM: Filter Cycle

## 1. State prediction (P step)

Predict the expected robot state.

 - Robot is supposed to only change its own position and not the position of the landmarks, so the part of the state changes:
$x$ , $\Sigma_{xx}$, $\Sigma_{mx}$, $\Sigma_{xm}$

 - The computation complexity $O(n)$


## 2. Measurement prediction (C step)

Predict the expected measurement given the belief of the robot state.
$\mu_m$ , $\Sigma_{mm}$

## 3. Measurement (C step)

Take the real measurement

## 4. Data association (C step)

(Assumed to be given, but could be difficult in practice.)

Associate the obtained measurement (of the landmarks) with the corresponding predicted landmark observation.

## 5. Update (C step)

Compute the difference between the obtained measurement and the predicted measurement, and update the state (mean and covariance matrix) via the Kalman Gain

The complete mean and covariance changes

The computation complexity $O(n^2)$


# EKF SLAM: Concrete Example

## Setup
 - Robot moves in the 2D plane
 - Velocity-based motion model
 - Robot observes point landmarks
 - Range-bearing sensor
 - Known data association
 - Known number of landmarks 

## Robot motion

Standard Odometry Model in 2D

$$
\begin{pmatrix}
x' \\
y' \\
\theta' \\
\end{pmatrix}
= 
\begin{pmatrix}
x \\
y \\
\theta \\
\end{pmatrix}
+

\begin{pmatrix}
- \frac{v_t}{w_t} \sin\theta + \frac{v_t}{w_t} \cos\theta \\
\frac{v_t}{w_t} \cos\theta - \frac{v_t}{w_t} \sin\theta \\
w_t \Delta t
\end{pmatrix}
$$


## Range-Bearing Observation

Standard Odometry Model in 2D with range-beraing observation $z_t^i = (r_t^i, \phi_t^i)^T$

Computing the observed location of landmark j with the estimated robot’s location and the relative measurement: 

$$
\begin{pmatrix}
\bar \mu_{j,x} \\
\bar \mu_{j,y} \\
\end{pmatrix}
=
\begin{pmatrix}
\bar \mu_{t,x} \\
\bar \mu_{t,y} \\
\end{pmatrix}
+
\begin{pmatrix}
r_t^i \cos(\phi_t^i + \bar \mu_{t, \theta}) \\
r_t^i \sin(\phi_t^i + \bar \mu_{t, \theta}) \\
\end{pmatrix}
$$


## State Initializaion

 - 2n+3 dimensions state (Robot pose + n landmarks)
 - Robot starts in its own reference frame (all n landmarks unknown)

$$
\mu_0 = 

\begin{pmatrix}
0 \\
0 \\ 
\vdots \\
0 \\
\end{pmatrix}
$$

$$
\Sigma_0 = 
\begin{pmatrix}
0 & 0 & 0 & 0 & \ldots & 0 \\
0 & 0 & 0 & 0 & \ldots & 0 \\
0 & 0 & 0 & 0 & \ldots & 0 \\
0 & 0 & 0 & \infty & \ldots & 0 \\
0 & 0 & 0 & \vdots & \ddots & \vdots \\
0 & 0 & 0 & 0 & \ldots & \infty \\
\end{pmatrix}
$$


![EKF SLAM Prediction](https://www.dropbox.com/s/d7h076z1e0l9omu/ekf_slam_prediction.png?dl=1)

![EKF SLAM Correction Step 1](https://www.dropbox.com/s/chetpivpl1esoth/ekf_slam_correction_1.png?dl=1)

![EKF SLAM Correction Step 2](https://www.dropbox.com/s/r2mbbg9i25fjiih/ekf_slam_correction_2.png?dl=1)

## Implementation Notes
 - Measurement update in a single step requires only one full belief update 
 - Always normalize the angular components
 - You may not need to create the matrices $ F $  explicitly (e.g., in Octave)


advantages and disadvantages
computational efficiency


