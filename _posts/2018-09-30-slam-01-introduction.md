---
layout: post
title:  Introduction to SLAM
subtitle: Course Note SLAM (Cyrill Stachniss)
author: Jihong Ju

---



Simultaneously localization and mapping (SLAM) is a chicken-and-egg problem.

**Localization** Estimate the path of the robot, i.e., a sequence of poses and locations,


$$
x_{0:T} = {x_{0}, x_{1}, x_{2}, ..., x_{T}}
$$


**Mapping** Build a map of the environment,


$$
m
$$


**SLAM** Simultaneously estimate the path of the robot and build a map of the environment given the robot's controls


$$
u_{1:T} = {u_{1}, u_{2}, ..., u_{T}}
$$



and observations


$$
z_{1;T} = {z_{1}, z_{2}, ..., z_{T}}
$$



# Probabilistic approaches
The real word is full of **uncertainty**, especially when we talk about robot perception and robot control. Graphical models are commonly used to represent uncertainty.

## Graphical models

**Full SLAM** estimates the full robot's path and the map:


$$
p(x_{0:T}, m | z_{1:T}, u_{1:T})
$$



![Graphical model of full SLAM](https://www.dropbox.com/s/vipk6ya0u74xnbs/full-slam.png?dl=1)



**Online SLAM** seeks to recover only the most recent pose:


$$
p(x_{t}, m | z_{1:t}, u_{1:t})
$$



It means marginalizing out the previous poses (recursively):


$$
p(x_{t}, m | z_{1:t}, u_{1:t}) = \int_{x_0} ... \int_{x_{t-1}} p(x_{0:t}, m | z_{1:t}, u_{1:t}) dx_{t-1} ... dx_{0}
$$


![Graphical model of online SLAM](https://www.dropbox.com/s/zcl4egxe6trgx1i/online-slam.png?dl=1)





## Motion Model

How does the robot move?

$$
p(x_{t} | x_{t}-1, u_{t})
$$

Example: Standard Odometry model

 - Robot moves $ (x, y, \theta) $ -> $ (x', y', \theta') $

 - Odometry information $ u = (\delta_{rot1}, \delta_{trans}, \delta_{rot2}) $



## Observation Model

How do I interpret the observations?

$$
p(z_{t} | x_{t})
$$

Example: Standard Observation Model




## Side notes

**Why SLAM is HARD?**

- Both the map and robot path are unknown, and estimations of map and robot path are correlated
- Observations to map association is also unknown. Wrong data association could cause catastrophical consequences

**Taxonomy of the SLAM problem**

 - Volumetric vs. feature-based SLAM
 - Topologic vs. geometric maps
 - Known vs. unknown correspondence
 - Static vs. dynamic environments
 - Small vs. large uncertainty
 - Active vs. passive SLAM
 - Any-time and any-space SLAM
 - Single-robot vs. multi-robot SLAM


