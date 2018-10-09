---
layout: post
title: EKF SLAM
author: Cyrill Stachniss's SLAM course

---

## SLAM

(S)multaneanously building a (m)ap of the world (a)nd (l)ocalizing the robot in the map

### Definition

Given robot controls $u_{1:T}$ and observations $z_{1:T}$, return a map $m$ and robot paths $x_{0:T}$ 

### Three Main Paradigms
 - Kalman filter
 - Particle filter
 - Graph-based


### [Recap](https://jihongju.github.io/2018/10/02/slam-lecture-note-03/): Bayes Filter 
Recursive filter with 
 - Prediction step

$$
\bar{bel}(x_t) = \int p(x_t \vert x_{t-1}, u_t) bel(x_{t-1}) dx_{t-1}
$$

 - Correction step

$$
bel(x_t) = p(z_t \vert x_t) \bar{bel}(x_t)
$$


## EKF for Online SLAM

Online SLAM:

$$
p(x_t, m \vert z_{1:t}, u_{1:t})
$$


![Graphical model of online SLAM](https://www.dropbox.com/s/zcl4egxe6trgx1i/online-slam.png?dl=1)


### [Recap](https://jihongju.github.io/2018/10/04/slam-lecture-note-04/#extended-kalman-filter-algorithm): EKF Algorithm

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


### EKF SLAM

Apply EKF on SLAM: Estimate robot pose and landmarks location

 - Assume known coresspondence




State
$$
x_t = (x, y, \theta, m_{1,x}, m_{1,y}, \dots, m_{n, x}, m_{n, y})
$$


State representation

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
$$


$$
\Sigma = 
\begin{pmatrix}
\Sigma_{xx} & \Sigma_{xm} \\
\Sigma_{mx} & \Sigma_{mm} \\
\end{pmatrix}
$$

### EKF SLAM: Filter Cycle

#### State prediction

$x$ , $\Sigma_{xx}$, $\Sigma_{mx}$, $\Sigma_{xm}$

#### Measurement prediction
$m$ , $\Sigma_{mm}$

#### Measurement

#### Data association

Associate observation prediction with the measured observation

Assumed easy, but could be difficult in practice

#### Update 


### EKF SLAM: Concrete Example

Setup
 - Robot moves in the 2D plane
 - Velocity-based motion model
 - Robot observes point landmarks
 - Range-bearing sensor
 - Known data association
 - Known number of landmarks 

Initializaion
 - Robot starts in its own reference frame (all n landmarks unknown)
 - 2n+2 dimensions

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

#### Implementation Notes
 - Measurement update in a single step requires only one full belief update 
 - Always normalize the angular components
 - You may not need to create the matrices $ F $  explicitly (e.g., in Octave)

