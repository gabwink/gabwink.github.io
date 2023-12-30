---
title: "Dynamic System Simulation"
excerpt: "<br/><img src='/images/jacksim.png' width='400' height='400'>"
collection: portfolio
---
## Simulation 
<img src="https://github.com/gabwink/gabwink.github.io/raw/master/images/jackinbox.gif" alt="Alt Text" width="400">

## Model of the System

<div style="text-align: center;">
    <br/><img src='/images/jackinbox.png' width='400px'><br>
</div>
Figure 1. Model of the jack in a cup system with all the frames that are utilized for the project

### System Assumptions
1. All collisions between the cup and the jack are elastic impacts
2. The mass of blue rods of the jack were assumed to be negligible. Therefore, jack is
modeled as four identical point masses equidistant from frame {a}
3. Both objects are modeled in gravity
4. Assume simultaneous impact does not occur (if it does the code uses the first
impact detected to solve the impact update equations).

### Configuration

$$
\begin{bmatrix}
    x_{box} \\
    y_{box} \\
    \theta_{box} \\
    x_{jack} \\
    y_{jack} \\
    \theta_{jack} \\
\end{bmatrix}
$$


### Drawing Description
The locations of the origins of frames {A} and {a} relative to the world frame {w} are represented by $(x_{box}, y_{box})$ and $(x_{jack}, y_{jack})$, respectively. The origin of these frames are at
the center of mass for the box and the jack. The $\theta$ configuration variable for the box and the jack is the planar rotation about the frames {A} and {a} relative to the world frame. A righthanded coordinate system in the xy plane is used, therefore, the positive z axis is out of the page (positive rotation ccw). In the code symbols are used to represent the geometry and mass of the system. These symbols are defined in the table below. 

Table 1. Description and values of Sympy symbols used in the code  

| Symbol | Description | Value |
|:--------|:-------:|--------:|
| M | total mass of box | 100 kg |
| m | total mass of jack |  1 kg |
| s | side length of square box | 3 m |
| Lj| length of the rod of the jack in Figure 1 this value is represented as the distance between frames {b} and {c} and frame {d} and {e} | 0.5 m |
| g | gravity | 9.8 (m/s) |

## Rigid Body Transformations
Transformations were represented with SE(3). They were used to represent the
configuration of the box and jack and to change the reference frame in which a vector or frame is represented. The rigid body transformations are described below. In the code, the
transformations have the same labels as the ones listed below.
### Subscript Naming Convention
* The frames are labeled so that the second subscript is the frame expressed relative to the first subscript. For example, g_wA represents frame {A} relative to frame {w}.
* The homogenous representation of the origin of frame {A} relative to {w} is represented in column 4 of the matrix.
* The orientation of frame {A} relative to {w} is represent by SO(3), the rows and columns of g_wA that are in bold below.  

$$
\begin{bmatrix}
    \mathbf{cos(\theta_{box}(t))} & \mathbf{-sin(\theta_{box}(t))} & \mathbf{0} & x_{b}(t) \\
    \mathbf{sin(\theta_{box}(t))} & \mathbf{cos(\theta_{box}(t))} & \mathbf{0} & y_{b}(t) \\
    \mathbf{0} & \mathbf{0} & \mathbf{1} & 0 \\
    0 & 0 & 0 & 1 \\
\end{bmatrix}
$$

* To change the reference frame of a vector or frame in the system the subscript cancelation rule is utilized For example, if $v_A$ is a vector expressed in {A} then, $[g_{wa}][v_A] = [v_w]$ multiplying by this transformation changed the reference frame of the vector from frame {A} to frame {w}.

#### Box Transformations (In code: cell 1, lines 169-188)
g_wA = g_box  
g_AC  
R_AD  
g_AD  
g_AE  
#### Jack Transformations (code: cell 1, lines 191-210)
g_wa = g_jack  
g_ab  
g_ac  
g_ad  
g_ae  
#### Transformations Used to Define Impact Constraints (code: cell 6, line 1-17)
These frames represent the jack frames {b}, {c}, {d}, and {e} relative to the box frame {E}  
g_wE = g_wA * g_AE  
g_Ew  
g_wb = g_wa * g_ab  
g_Eb  
g_wc = g_wa * g_ac  
g_Ec  
g_wd = g_wa * g_ad  
g_Ed  
g_we = g_wa * g_ae  
g_Ee  
## Calculation Steps
### Euler Lagrange Equations
1. Chose the configuration q (define above).
2. Computed the kinetic energy for each body, the jack and the box.  

    $$
    \text{KE}=\frac{1}{2}(V^b)^T \begin{bmatrix} mI_{nxn} & 0\\ 0 & \mathbf{I} \\ \end{bmatrix} V^b
    $$
    $mI_{nxn}$ is the inertia due to mass translation for each body. The mass for both bodies is described above in Table 1. $\mathbf{I}$ is the rotational inertia (inertia tensor). The inertia tensor is diagonal $\mathbf{I} = \text{diag}(m,m,m,J_1,J_2,J_3)$ because for both bodies it is obtained with respect to the **a** body frame at the center of mass and with axes aligned with the principal axes of inertia. For the box this frame, represented in the world frame, is g_wA = g_box and for the jack this frame is g_wa = g_jack.

    The system is planar, therefore, there is no rotation about the x and y principal axes.
    Therefore, $J_1$ and $J_2$ terms of the inertia tensor do not contribute to the magnitude of kinetic energy and are represented as symbols in the code. The $J_3$ term for the box and the jack was computed using the inertia_box and inertia_jack functions in the code, respectively. For the jack,  

    $$
    J_3 = (4)\frac{1}{2} \frac{m}{4}(Lj/2)^2= \frac{1}{2}m(Lj/2)^2
    $$

    the moment of inertia of four point masses, each with mass $\frac{m}{4}$, about the z axis of the {a} frame. For the box,

    $$
    J_3 = \frac{M}{12}[(2s^2)-(2t^2)]
    $$ 

    the inertia of a rectangular ring about the z axis of the frame {A} (1). Where, t is the thickness of the rectangular box segments.

    The body angular velocity $V^b$ for both bodies was computed.  

    $$
    \hat{V}^b =(g^{-1}\dot{g})
    $$

    For the box g = g_wA and for the jack g =g_wa. The calculation above maps the velocities in frame {w} to the velocities in frame {A} (box) or frame {a} jack. The unhat function in the code “unhats" the 4x4 matrix $\hat{V}^b$ to get an element of $ℝ^6$, the body velocity vector. The kinetic energy was then computed for both the box and the jack with the terms described above. Then, the resulting kinetic energies for both objects were added to compute the total kinetic energy of the system.

3. Computed the potential energy for each rigid body. To calculate the potential energy the y component of the {A} frame (for the box) and the {a} frame (for the jack) was extracted from the g_wA and g_wa matrices, respectively. Then, the potential energy was found using:  

    $$
    V = M*g*py_{box} + m*g*py_{jack}
    $$

4. Computed the Lagrangian for the system:  
   
    $$
    L = KE - V
    $$

5. Found the left-hand-side of the forced Euler-Lagrange Equations:     

    $$
    \frac{d}{dt}\frac{\partial{L}}{\partial{\dot{q}}} - \frac{\partial{L}}{\partial{q}}
    $$

### External Forces
An external force is applied the box (cup) object of the system. This force is applied at the center of mass of the cup.  

$$
\text{external force shaking the cup in the y } F_{ext} = \begin{bmatrix} 0 \\ F_y \\ 0\\ 0\\ 0\\ 0\\ \end{bmatrix}
$$

The $F_{ext}$ vector is the right-hand-side of the forced Euler-Lagrange Equations. The external force applied to the cup was:  

$$
F_{ext} = Mg - 10 * M * \cos{(5 * t)}
$$

The resulting equation of motion for the cup in the vertical direction:  

$$
\ddot{y}_b = 10\cos{(5t)}
$$

This force was applied to oscillate the cup. The cosine term was included to invoke oscillation and the Mg term was included to counteract the force of gravity.

### Impact Update Laws
To solve the impact update rule two sets of equations were used:  

$$
\frac{\partial{L}}{\partial{\dot{q}}} \left. \vphantom{\int_a^b} \right|_{\tau^-}^{\tau^+} = \lambda \frac{\partial{\phi}}{\partial{q}}  \\
H = 0
$$

H is the Hamiltonian of the system, the energy update, and the equation above is the momentum update.

The system described had 16 potential collisions. However, all 16 are not implemented
each timestep. In the code, the impact_conditions function loops through each impact
constraint and checks if any impacts are detected for the current state of the system. If impact is detected, then the impact equations above are solved for $\dot{q}$ and $\lambda$.

To define the impact constraint of the system the four mass frames {b}, {c}, {d}, {e} were
defined relative to the {E} frame of the box. The x and y values were extracted from these
transformations. They represent the x and y position of each mass of the jack relative to frame {E} of the box. Each mass of the jack has four constraints:  

$$
\phi = x - 0,  \\
\phi = y – 0,  \\
\phi = x – s,  \\
\phi  = y – s,  
$$

If the x or y coordinate of the origin of any mass frame was in contact (on the same line)
as any of the box boundaries, then the impact was detected.

## Code Results Discussion
The system is simulated for three seconds. In the simulation the jack is inside the box
and is not in contact with the box at $t_0$, the start of the simulation. The jack then falls due to the force of gravity on the jack and eventually collides with the bottom of the box. The box is also experiencing a downward gravitational force and an external force. This external force causes the box to oscillate in the vertical direction (y of box frame). When the jack collides with the box, both objects experience an instantaneous change velocity due to the forces that they apply on each other. In total, 10 collisions are detected during the simulation.

To justify the accuracy of the dynamics of the simulated system various tests were
performed. First, the external force (the right-hand-side of the Forced Euler Lagrange
Equations) was set to 0. Then, the Hamiltonian was plotted.

<div style="text-align: center;">
    <br/><img src='/images/fig2_port4.png' width='400px'><br>
</div>
Figure 2. Hamiltonian for the jack and box system with no external forces

The Hamiltonian of the system is nearly constant. The behavior of the Hamiltonian is
reasonable. The collisions in the system are elastic and there are no external forces, therefore, conservation laws tell us that Figure 2 above is the qualitative result expected. In addition, the trajectory of this system (with no external force) was plotted. This plot was used to check the Lagrangian of the system. It is easy to predict the expected trajectory of a rigid falling in gravity.

<div style="text-align: center;">
    <br/><img src='/images/fig3_port4.png' width='400px'><br>
</div>
Figure 3. Box and jack system falling in gravity

The behavior for this plot was also as expected the jack and the box are falling with a
negative acceleration of 9.8 $m/s^2$. Another reason why the behavior of the system is reasonable is it is evident from the animation that when the jack collides with the box the jack exhibits a greater acceleration than the box. This is as expected because the jack is significantly lighter than the box. 

## Resources 
The code for this simulation is available upon request. 