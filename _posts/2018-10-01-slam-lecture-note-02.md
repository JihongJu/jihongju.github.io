---
layout: post
title: Homogeneous coordinates
subtitle: Course Note SLAM (by Cyrill Stachniss)
author: Jihong Ju

---


## Why?
 - Used in projective geometry
 - Simpler formulas
 - Points at infinity can be represented with finite coordinate
 - Affine transformation can be represented with a single matrix

## Definition
The representation x of a geometric object is homogeneous if x and $\lambda x$ represent the same object for $ \lambda x \neq 0 $, i.e. homogeneous coordinates are equivalent up to scale.


## Transformations
Projective transformation:
$$
x' = M x
$$


- Translation: 3 params

$$
M = \lambda
\begin{bmatrix}
I   & t \\
0^T & 1
\end{bmatrix}
$$

- Rotationn: 3 params

$$
M = \lambda
\begin{bmatrix}
R & 0 \\
0^T & 1
\end{bmatrix}
$$


- Figid body transformation: 6 params

$$
M = \lambda
\begin{bmatrix}
R & t \\
0^T & 1 
\end{bmatrix}
$$


- Similarity transformation: 7 params
3 trans + 3 rot + 1 scale

$$
M = \lambda
\begin{bmatrix}
mR & t \\
0^T & 1
\end{bmatrix}
$$

- Affine transoformation: 12 params
3 trans + 3 rot + 3 scale + 3 sheer

$$
M = \lambda
\begin{bmatrix}
A & t \\
0^T & 1
\end{bmatrix}
$$



### Properties

 - Invert a transformation

$$
x' = M x
$$

$$
x = M^{-1} x'
$$

 - Chaining transoformations via matrix products (not commutative)

$$
M_1 M_2 x \neq M_2 M_1 x
$$

