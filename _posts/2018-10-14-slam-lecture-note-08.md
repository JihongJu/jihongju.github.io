---
layout: post
title: Sparse Extended Information Filter
author: Cyrill Stachniss SLAM course

---


* TOC
{:toc}



# Motivation

![covmat-vs-infomat](https://www.dropbox.com/s/yalswmbiblj0z2s/cov_mat_vs_info_mat.png?dl=1)

Unlike the convariance matrix (middle), the information matrix (right) has most of its off-diganoal elements close to 0.

Information matrix itself is not sparse but can be __approximated__ with sparse matrix.

__sparse matrix__: finite number of non-zero off-diagonals, independent of the matrix size


# Graph interpretation of information matrix

Information matrix can be interpreted as MRF, where nodes denotes random variables and edges describes dependencies between variables. 

![infomat-as-mrf](https://www.dropbox.com/s/d8hqlve9ar7tttw/infomat_graph.png?dl=1)

 - $\Omega_{ij}$ tells the strength of a link between node $i$ and $j$ 
 - $\Omega_{ij}=0$ indicate conditional independence of the two random variables, i.e. missing links


## Effect of measurement update

![measure-update](https://www.dropbox.com/s/1l45uzyl0s70woo/measurement_update.gif?dl=1)


## Effect of motion update


![motion-update](https://www.dropbox.com/s/7beq4zo44vkauky/motion_update.gif?dl=1)


# Sparsification


![sparsification](https://www.dropbox.com/s/839q7zh0fklcpkb/sparsification.gif?dl=1)

Keep a bank of activate landmarks and remove links between the robot pose and passive landmarks.

__Activate landmarks__: A (fixed number) subset of all landmarks, including all the currently observed one

__Passive landmarks__: All others.

The robot’s pose is linked to the active landmarks only, and the landmarks have only links to nearby landmarks (landmarks that have been active at the same time)

## Sparsification in Every Step

SEIF SLAM conducts a sparsification steps in each iteration


# Four steps of SEIF SLAM

![seif-slam](https://www.dropbox.com/s/3z0vzljxjq3j8ye/seif_slam.png?dl=1)


Two nodes are not connected: 
 - The two randon variables are conditionally independent of each other given we know the rest of the variables.
 - They could still be dependent of each other if the other variables are unknown.


The two landmarks $m1$, $m2$ are independent of each other given I know the robot pose.


Sparsitification is an approximate. Lose accuracy in change of computational efficiency if you do that.



buffer for activate landmarks
