---
layout: post
title: Introduction to Bayes Filter
author: Cyrill Stachniss's SLAM course

---


## State Estimation

Estimate the state of x of a system given obesrvatioins z and controls u

$$
p(x | z, u)
$$


## Recursive Bayes Filter


Define belief

$$
bel(x_t) = p(x_{t} | z_{1:t}, u_{1:t}) \\
$$


With Bayes rule

$$
= \eta p(z_t | x_{t}, z_{1:t-1}, u_{1:t}) p(x_{t}|z_{1:t-1},u_{1:t})
$$

With Markov assumption:

$$
= \eta p(z_t | x_{t}) p(x_{t}|z_{1:t-1},u_{1:t})
$$


With law of total probability

$$
= \eta p(z_t | x_t) \int_{x_{t-1}} p(x_t | x_{t-1}, z_{1:t-1}, u_{1:t}) p(x_{t-1} | z_{1:t-1}, u_{1:t}) dx_{t-1}
$$

With again Markov assumption

$$
= \eta p(z_t | x_t) \int_{x_{t-1}} p(x_t | x_{t-1}, u_t) p(x_{t-1} | z_{1:t-1}, u_{1:t}) dx_{t-1}
$$

With the assumption the current state $ x_{t} $ is independent of the future controls $ u_{t+1} $

$$
= \eta p(z_t | x_{t}) \int_{x_{t-1}} p(x_t | x_{t-1}, u_t) p(x_{t-1} | z_{1:t-1}, u_{1:t-1}) dx_{t-1} \\
= \eta p(z_t | x_{t}) \int_{x_{t-1}} p(x_t | x_{t-1}, u_t) bel(x_{t-1}) dx_{t-1}
$$

A recursive term!


### Prediction and correction step

Prediction step

$$
\bar{bel}(x_t) = \int p(x_t \vert x_{t-1}, u_t) bel(x_{t-1}) dx_{t-1}
$$

where $ p(x_t \vert x_{t-1}, u_t) $ is so-called the motion model


Correction step

$$
bel(x_t) = p(z_t \vert x_t) \bar{bel}(x_t)
$$

where $ p(z_t \vert x_t) $ is the observation model.

### Different Realizations

Framework for recursive state estimation

Different relaization and properties

 - Linear vs. non-linear models for motion/observation models
 - Gaussian distribution only?
 - Parametric filters


Kalman filter & friends
 - Gaussians
 - Linear or linearized models (note: motion is often non-linear because of rotations)

Particle fileter
 - Non-parametric
 - Arbitrary


## Robot Motion models

 - Robot motion is inherently uncertain, How to model uncertainty?


### Probabilistic Motion Models

Specify a posterior probabiilty that carries the robot from x to x':
$$
p(x_t | x_{t-1}, u_t)
$$

### Odometry-based


Standard odometry model

#TODO the nice diagram

Probability Dist
 - Noise in $ (x, y, \theta) $, e.g. $ u \sim N(0, \Sigma) $

#TODO the nice diagram and examples

### Velocity-based

$ (x, y, \theta) $ to $ (x', y', \theta') $ with translation velocity v and rotation velocity w. 


 - Problem: No arc could describe $ \theta' \neq \theta $
 - Fix: Extra term for the final rotation


## Sensor Model

$$
p(z_t|x_t)
$$

### Model for Laser Scanners

Measure Time-of-Flight

Scan z consists of k measurements

$$
z_t = (z_t^1, ..., z_t^k)
$$

$$
p(z_t | x_t, m) = \prod_{i=1}^k p(z_t^i | x_t, m)
$$

Assumption: Individual measurements are independent given the robot position

### Beam-Endpoint Model
Physically flaw bu_t efficient

### Ray-cast Model

Mixture of four models: exponential decay in distance, gaussian at obstacle, delta at the max-distance and a uniform distribution.


### Model for perceiving landmarks with Range-Bearing Sensors

Range-Bearing

$$
z_t^i = (r_t^i, \phi_t^i)^T
$$

Robot's pose 

$$
(x, y, \theta)^T
$$

Observation of feature j at location $ (m_{j,x}, m_{j,y}) $

## Reading materials

Bayes filter
 - PR Chapter 2
 - Introduction to Mobile Robotics Chapter 5

Motion and sensor models
 - PR 5 & 6
 - Course 6 & 7
