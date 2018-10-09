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


### Bayes Filter [(Recap)](https://jihongju.github.io/2018/10/02/slam-lecture-note-03/)
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
