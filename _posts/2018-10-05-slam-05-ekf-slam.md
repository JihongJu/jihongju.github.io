---
layout: post
title: EKF SLAM
subtitle: Course Note SLAM (by Cyrill Stachniss)
author: Jihong Ju
redirect_from: "/2018/10/05/slam-lecture-note-05/"

---

* TOC 
{:toc}
## Simultaneous localization and mapping (SLAM)

SLAM means simultaneously building a map of the world and localizing the robot in the map.

**Formal Definition**

>  Given the robot controls $u_{1:T}$ and observations $z_{1:T}$, return a map of the world $m$ and the robot paths $x_{0:T}$ 

**Three Main Paradigms**

 - Bayes filter
 - Particle filter
 - Graph-based

In this post, we will focus on the [Bayes filter](https://jihongju.github.io/2018/10/02/slam-lecture-note-03/), or more specifically, the [Kalman filter](https://jihongju.github.io/2018/10/04/slam-lecture-note-04/) paradigm.

**Bayes Filter**

A recursive filter for state estimation with two steps:

 - Prediction step

$$
\bar{belief}(x_t) = \int p(x_t \vert x_{t-1}, u_t) belief(x_{t-1}) dx_{t-1}
$$

 - Correction step

$$
belief(x_t) = p(z_t \vert x_t) \bar{belief}(x_t)
$$

With the assumption of Gaussian distribution and local linearity, the Bayes filter used in a SLAM system often boils down to an [Extended Kalman Filter](https://jihongju.github.io/2018/10/04/slam-lecture-note-04/#extended-kalman-filter-algorithm) (EKF).

**The Extended Kalman Filter (EKF) Algorithm**

![ekf-slam](https://www.dropbox.com/s/9h5ahlmewpxbr62/ekf_slam.png?dl=1)

## Extended Kalman Filter (EKF) for Online SLAM

Apply EKF on SLAM to estimate the robot pose and the landmarks location in the environment.

**Online SLAM**
$$
p(x_t, m \vert z_{1:t}, u_{1:t})
$$


![Graphical model of online SLAM](https://www.dropbox.com/s/zcl4egxe6trgx1i/online-slam.png?dl=1)



**State representation**

Assuming known landmark correspondences, the state, representing the robot pose and the location of n landmarks in 2D space, is

$$
x_t = (x, y, \theta, m_{1,x}, m_{1,y}, \dots, m_{n, x}, m_{n, y})
$$

Assuming the robot pose and the landmark locations are both Gaussian distribution, the probabilistic representation of the state is the mean and the variance:


$$
\mu = (x, y, \theta, m_{1,x}, m_{1,y}, \dots, m_{n, x}, m_{n, y})
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



Or, in simpler form,


$$
\mu = 
\begin{pmatrix}
x \\
m \\
\end{pmatrix}
$$

$$
\Sigma = 
\begin{pmatrix}
\Sigma_{xx} & \Sigma_{xm} \\
\Sigma_{mx} & \Sigma_{mm} \\
\end{pmatrix}
$$



## EKF SLAM: Filter Cycle

1. **Robot pose prediction (P step)**: Predict the expected robot state given the previous robot pose and the robot odometry
   - Robot is only supposed to change its own pose and not the landmarks positions, so the parts of the state to change at this step are: $x$ , $\Sigma_{xx}$, $\Sigma_{mx}$, $\Sigma_{xm}$
   - Computation complexity $O(n)$


2. **Measurement prediction (C step)**: Predict the expected measurement given the belief of the robot state.
   - Parts of the state to update: $\mu_m$ , $\Sigma_{mm}$
3. **Measurement (C step)**: Take the real measurement
4. **Data association (C step)**: Associate the obtained measurement (of the landmarks) with the corresponding predicted landmark observation.
5. **Update (C step)**: Compute the difference between the obtained measurement and the predicted measurement, and update the state (mean and covariance matrix) via the Kalman Gain.
   - The complete mean and covariance changes
   - Computation complexity $O(n^2)$


## EKF SLAM: Concrete Example

**Setup**

 - Robot moves in the 2D plane
 - Velocity-based motion model
 - Robot observes point landmarks
 - Range-bearing sensor
 - Known data association
 - Known number of landmarks 

**Robot motion** Standard Odometry Model in 2D

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

where $v_t$ is the translation velocity, $w_t$  the rotation velocity and $\Delta t$ the time interval between time steps.

**Range-Bearing Observation**
Standard Odometry Model in 2D with range-bearing observation
$$
z_t^i = (r_t^i, \phi_t^i)^T
$$

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

**State Initialization**

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

**Prediction step**

![EKF SLAM Prediction](https://www.dropbox.com/s/d7h076z1e0l9omu/ekf_slam_prediction.png?dl=1)

(2) Use $F_x$ to map from 3 dimension to 2n+3 dimension because the motion model $g$ only affects the robot motion and not the landmarks. 

(3) Predict mean with Robot motion model $g$ 

(4) Jacobian
$$
G_t = 
\begin{pmatrix}
G_t^x & 0 \\ 
0 & I \\ 
\end{pmatrix}
$$

where 
$$
G_t^x = \frac{\partial (x', y', \theta ')^T}{\partial (x, y, \theta)^T} 
$$

(5) Predict covariance with Jacobian and motion noise

**Correction step**

 - Known data association
 - Compute the Jacobian of $h$
 - Proceed with computing the Kalman gain



![EKF SLAM Correction Step 1](https://www.dropbox.com/s/chetpivpl1esoth/ekf_slam_correction_1.png?dl=1)

(6) Known measurement noise

(7) For each of the observed feature

(8) $c_t^i = j$: i-th measurement at time t observes the landmark with index j

(9-11) Initialize landmark if unobserved

(12-14) Compute the expected observation according to the current estimation with $\hat z_t^i = h(\bar \mu_t)$


![EKF SLAM Correction Step 2](https://www.dropbox.com/s/r2mbbg9i25fjiih/ekf_slam_correction_2.png?dl=1)

(15) Use $F_{x,j}$ to map from 2 to 2n+3 dim for the j-th landmark 

(16) Compute Jacobian of measurement model:
$$
H_t^i = \frac{\partial h(\bar \mu_t)}{\partial \bar \mu_t}
$$


Note that Jocabian for j-th landmark is low-dimensional: $(2 \times 5)$, $(r, \phi)$ with respect to $(x, y, \theta, m_{j, x}, m_{j, y})$.

(17) Compute the Kalman gain

(18-19) Correct the state mean and covariance with Kalman gain

**Implementation Notes**

 - Measurement update in a single step requires only one full belief update 
 - Always normalize the angular components
 - You may not need to create the matrices $ F $  explicitly (e.g., in Octave)

**Complexity**

 - $O(n^3)$ depends only on the measurement dimensionality
 - Cost per step: dominated by the number of landmarks $O(n^2)$
 - Memory consumption $O(n^2)$
 - The EKF becomes computationally intractable for large maps! -> Sparse Extended Information Filter (SEIF) SLAM

**Correlation**

The correlation between the robot’s pose and the landmarks becomes fully correlated and cannot be ignored. Assuming independence generates too optimistic estimates of the uncertainty.
 -> SEIF SLAM

![covmat-update](https://www.dropbox.com/s/e81g6gs5gwzey9e/covmat_update.gif?dl=1)

**Uncertainties**

The determinant of any sub-matrix of the map covariance matrix decreases monotonically as robot moves and new observations made. That's why new landmarks are initialized with maximum uncertainty.

In the limit, the covariance associated with any single landmark location estimate is determined only by the initial covariance in the vehicle location estimate.

**Loop closing**

 - Loop closing means recognizing an already mapped area.
 - Could fail with data association under
 	+ high ambiguity
 	+ possible environment symmetries
 - Uncertainties collapse after a loop closure (whether the closure was correct or not)!
 - Wrong loop closures lead to filter divergence

**Summary**

 - Convergence proof for the linear Gaussian case
 - Can diverge if non-linearities are large (and the reality is non-linear...)
 - Can deal with single mode
 - Successful in medium-scale scene
 - Approximations exists to reduce the computational complexity

