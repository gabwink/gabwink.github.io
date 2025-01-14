---
title: "Filters"
excerpt: "This post features practical implementations of Kalman and Particle filters—widely employed algorithms in robotics to address uncertainties within the environment. By effectively accommodating these uncertainties, the filters play a crucial role in enhancing state estimation and control, contributing to the robust performance of robotic systems.   
<br/><img src='/images/particlefilter.png' width='400'>"
collection: portfolio
---

## Particle Filter 
A particle filter implemented for a differential drive vehicle subject to the dynamics, 


$\begin{bmatrix} \dot{x} \\ \dot{y} \\ \dot{\theta} \end{bmatrix} = \begin{bmatrix} cos(\theta)u_1 \\ sin(\theta)u_1  \\ u_2  \end{bmatrix}$ , $(x(0), y(0), \theta(0)) = (0, 0, \pi/2)$


using dt = 0.1 and process noise and measurement noise normally distributed with a variance of 0.02.

### Result
<br/><img src='/images/particlefilter.png' width='300px' height='200px'><br>
Figure 1: Each color represents a snapshot of the particles at a particular time. The black line is the state estimate of x and y every dt seconds.


## Kalman Filter 
The Kalman filter
provides an algorithm to determine an estimate of the state $\hat{x}_t$ t by combining models of the system and noisy measurements of certain parameters or linear functions of parameters<br>

The Kalman Filter Algorithm involves two tages:  
1. prediction
2. measurement update 

- must cite article -

### Resources 
The code and a more comprehensive Readme file can be found [here](https://github.com/gabwink/estimation)