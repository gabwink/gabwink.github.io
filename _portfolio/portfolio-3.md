---
title: "Filters"
excerpt: "This post features practical implementations of Kalman and Particle filters—widely employed algorithms in robotics to address uncertainties within the environment. By effectively accommodating these uncertainties, the filters play a crucial role in enhancing state estimation and control, contributing to the robust performance of robotic systems. <br/><img src='/images/particlefilter.png'>"
collection: portfolio
---

## Particle Filter 
A particle filter implemented for a differential drive vehicle subject to the dynamics, 

```math
\begin{bmatrix} \dot{x} \\ \dot{y} \\ \dot{\theta} \end{bmatrix} = \begin{bmatrix} cos(\theta)u_1 \\ sin(\theta)u_1  \\ u_2  \end{bmatrix}$ , $(x(0), y(0), \theta(0)) = (0, 0, \pi/2)$
```

using dt = 0.1 and process noise and measurement noise normally distributed with a variance of 0.02.

### Algorithm 

$X_t = []$  
$\bar{X_t} = []$  
**for** m = 1, ..., M **do**  
* sample $x^{[m]}_t$ from $p(x_t|u_t,x^{[m]}_t)$  
* $w^{[m]}_t = p(z_t|x^{[m]}_t)$
* add the pair $(x^{[m]}_t, w^{[m]}_t)$ to $\bar{X_t}$  

**end for**  
**for** m = 1, ..., M **do**  
* draw *i* with probability $w^{[m]}_t$ 
* add $x^{[i]}_t$ to $X_t$ 

**end for**  
Return $X_t$

### Result
<br/><img src='/images/particlefilter.png'>
Figure 1: Each color represents a snapshot of the particles at a particular time. The black line is the state estimate of x and y every dt seconds.

### Resources 
The code and a more comprehensive Readme file can be found [here](https://github.com/gabwink/estimation)