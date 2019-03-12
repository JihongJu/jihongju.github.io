---
layout: post
title:  Introduction to Robotic Mapping
author: Course Note SLAM (Cyrill Stachniss)

---

## Related terms
 - state estimation, localization, mapping, slam
 - navigation, motion planning (e.g. A-star)

## SLAM
Localization: Estimate the robot's path, i.e., a sequence of poses and locations

Mapping: Build a map, i.e., a model of the environment

slam is a chicken-and-egg problem

### Definition
Given:
The robot's controls

$$
u_{1:T} = {u_{1}, u_{2}, ..., u_{T}}
$$

Observations

$$
z_{1;T} = {z_{1}, z_{2}, ..., z_{T}}
$$

Map of the environment

$$
m
$$

Path of the robot

$$
x_{0:T} = {x_{0}, x_{1}, x_{2}, ..., x_{T}}
$$

### Probabilistic approaches
To represents the uncertainty

$$
p(x_{0:T}, m | z_{1:T}, u_{1:T})
$$

![Graphical model of full SLAM](https://www.dropbox.com/s/vipk6ya0u74xnbs/full-slam.png?dl=1)

Online SLAM seeks to recover only the most recent pose

$$
p(x_{t}, m | z_{1:t}, u_{1:t})
$$

![Graphical model of online SLAM](https://www.dropbox.com/s/zcl4egxe6trgx1i/online-slam.png?dl=1)

$$
p(x_{t}, m | z_{1:t}, u_{1:t}) = \int_{x_0} ... \int_{x_{t-1}} p(x_{0:t}, m | z_{1:t}, u_{1:t}) dx_{t-1} ... dx_{0}
$$

### Why SLAM is HARD
- Both the map and robot path are unknown, and estimations of map and robot path are correlated
- Observations to map association is also unknown

### Taxonomy

 - Volumetric vs. feature-based SLAM
 - Topologic vs. geometric maps
 - Known vs. unknown correspondence
 - Static vs. dynamic environments
 - Small vs. large uncertainty
 - Active vs. passive SLAM
 - Any-time and any-space SLAM
 - Single-robot vs. multi-robot SLAM


## Motion Model
How does the robot move?

$$
p(x_{t} | x_{t}-1, u_{t})
$$

Gaussian model, Non-Gaussian models

__Standard Odometry Model__ #TODO Introduction to Mobile Robotics Course Chapter 6 or Probablics Robotics Chapter 5

 - Robot moves $ (x, y, \theta) $ -> $ (x', y', \theta') $
 - Odometry info $ u = (\delta_{rot1}, \delta_{trans}, \delta_{rot2}) $

## Observation Model
How do I interpret the observations?

$$
p(z_{t} | x_{t})
$$

__Standar Odometry Model__ #TODO Introduction to Mobile Robotics Course Chapter 7 or Probablics Robotics Chapter 6

## Side notes
__odometry__: example: motion measurements with motor sensors

