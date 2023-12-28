---
title: "Dynamic System Simulation"
excerpt: "<br/><img src='/images/jacksim.png' width='400' height='400'>"
collection: portfolio
---
## Simulation 
![Alt Text](https://github.com/gabwink/gabwink.github.io/raw/master/images/jackinbox.gif)

## Model of the System

<br/><img src='/images/jackinbox.png' width='300px' height='200px'><br>
Figure 1. Model of the jack in a cup system with all the frames that are utilized for the project

### System Assumptions
1. All collisions between the cup and the jack are elastic impacts
2. The mass of blue rods of the jack were assumed to be negligible. Therefore, jack is
modeled as four identical point masses equidistant from frame {a}
1. Both objects are modeled in gravity
2. Assume simultaneous impact does not occur (if it does the code uses the first
impact detected to solve the impact update equations).

### Configuration

$$
\begin{bmatrix}
    x_{box} \\
    y_{box} \\
    \theta_{box} \\
\end{bmatrix}
$$


### Drawing Description
The locations of the origins of frames {A} and {a} relative to the world frame {w} are
represented by (�&'(, �&'() and (�+,-., �+,-.) , respectively. The origin of these frames are at
the center of mass for the box and the jack. The � configuration variable for the box and the
jack is the planar rotation about the frames {A} and {a} relative to the world frame. A righthanded coordinate system in the xy plane is used, therefore, the positive z axis is out of the
page (positive rotation ccw).
In the code symbols are used to represent the geometry and mass of the system. These
symbols are defined in the table below.
Table 1. Description and values of Sympy symbols used in the code
| Symbol | Description | Value |
|----------|----------|----------|
| M | total mass of box | 100 kg |
| m | total mass of jack |  1 kg |
| s | side length of square box | 3 m |
| Lj| length of the rod of the jack in Figure one this value
is represented as the distance between frames {b}
and {c} and frames {d} and {e} | 0.5 m |
| g | gravity | 9.8 (m/s) |
| s | Row 3, Col 2 | Row 3, Col 3 |




## Rigid Body Transformations
Transformations were represented with SE(3). They were used to represent the
configuration of the box and jack and to change the reference frame in which a vector or frame
is represented. The rigid body transformations are described below. In the code, the
transformations have the same labels as the ones listed below.
### Subscript Naming Convention
* The frames are label so that the second subscript is the frame expressed relative to the first subscript. For example, g_wA represents frame {A} relative to frame {w}.
* The homogenous representation of the origin of frame {A} relative to {w} is represented in column 4.
* The orientation of frame {A} relative to {w} is represent by SO(3), the rows and columns of g_wA that are in bold below.
* To change the reference frame of a vector or frame in the system subscript cancelation rule is utilized. For example, if �7 is a vector expressed in {A} then, [g_wA] [��] = [��]
multiplying by this transformation changed the reference frame of the vector from frame {A} to frame {w}.

### Box Transformations (In code: cell 1, lines 169-188)
g_wA = g_box
g_AC
R_AD
g_AD
g_AE
### Jack Transformations (code: cell 1, lines 191-210)
g_wa = g_jack
g_ab
g_ac
g_ad
g_ae
### Transformations Used to Define Impact Constraints (code: cell 6, line 1-17)
These frames represent the jack frames {b}, {c}, {d}, and {e} relative to the box frame {E}
g_wE = g_wA*g_AE
g_Ew
g_wb = g_wa*g_ab
g_Eb
g_wc = g_wa*g_ac
g_Ec
g_wd = g_wa*g_ad
g_Ed
g_we = g_wa*g_ae
g_Ee
## Calculation Steps
### Euler Lagrange Equations
1. Chose the configuration q (define above).
2. Computed the kinetic energy for each body, the jack and the box. 
KE = Q
R (�&)T U
��X×X 0
0 �[ �&
��X×X is the inertia due to mass translation for each body. The mass for both bodies is
described above in Table 1. � is the rotational inertia (inertia tensor). The inertia tensor is
diagonal �\×\=diag(m,m,m, �Q,�R,�^) because for both bodies it is obtained with respect to the
a body frame at the center of mass and with axes aligned with the principal axes of inertia. For
the box this frame, represented in the world frame, is g_wA = g_box and for the jack this frame
is g_wa = g_jack.
The system is planar, therefore, there is no rotation about the x and y principal axes.
Therefore, �Q and �R terms of the inertia tensor do not contribute to the magnitude of kinetic
energy and are represented as symbols in the code. The �^ term for the box and the jack was
computed using the inertia_box and inertia_jack functions in the code, respectively. For the
jack,
�^ = (4)
1
2
�
4 (��/2)R = 1
2 �(��/2)R
the moment of inertia of four point masses, each with mass e
f , about the z axis of the {a} frame.
For the box,
�^ = �
12 [(2�R) − (2�R)]
the inertia of a rectangular ring about the z axis of the frame {A} (1). Where, t is the thickness of
the rectangular box segments.
The body angular velocity �& for both bodies was computed.
�
i&=(�jQ�̇)
For the box g = g_wA and for the jack g =g_wa. The calculation above maps the velocities in
frame {w} to the velocities in frame {A} (box) or frame {a} jack. The unhat function in the code
“unhats" the 4x4 matrix �
i& to get an element of ℝ\ , the body velocity vector. The kinetic
energy was then computed for both the box and the jack with the terms described above. Then,
the resulting kinetic energies for both objects were added to compute the total kinetic energy
of the system.
1. Computed the potential energy for each rigid body. To calculate the potential energy the y
component of the {A} frame (for the box) and the {a} frame (for the jack) was extracted from
the g_wA and g_wa matrices, respectively. Then, the potential energy was found using:
V = M*g*py_box + m*g*py_jack
1. Computed the Lagrangian for the system:
� = �� – �
1. Found the left-hand-side of the forced Euler-Lagrange Equations:
�
��
��
��̇
− ��
��
### External Forces
An external force is applied the box (cup) object of the system. This force is applied at the
center of mass of the cup.
external force shaking the cup in the y �s(t =
⎣
⎢
⎢
⎢
⎢
⎡
0
�u
0
0
0
0 ⎦
⎥
⎥
⎥
⎥
⎤
The �s(t vector is the right-hand-side of the forced Euler-Lagrange Equations. The external
force applied to the cup was:
�s(t = �� − 10 ∗ � ∗ ���(5 ∗ �)
The resulting equation of motion for the cup in the vertical direction:
�̈
& = 10���(5�)
This force was applied to oscillate the cup. The cosine term was included to invoke oscillation
and the Mg term was included to counteract the force of gravity.
### Impact Update Laws
To solve the impact update rule two sets of equations were used:
��
��̇
|�−�+ = � ��
��
H = 0
H is the Hamiltonian of the system, the energy update, and the equation above is the momentum
update.
The system described had 16 potential collisions. However, all 16 are not implemented
each timestep. In the code, the impact_conditions function loops through each impact
constraint and checks if any impacts are detected for the current state of the system. If impact
is detected, then the impact equations above are solved for �̇ and �.
To define the impact constraint of the system the four mass frames {b}, {c}, {d}, {e} were
defined relative to the {E} frame of the box. The x and y values were extracted from these
transformations. They represent the x and y position of each mass of the jack relative to frame
{E} of the box. Each mass of the jack has four constraints:
� = x - 0,
� = y – 0,
� = x – s,
� = y – s,
If the x or y coordinate of the origin of any mass frame was in contact (on the same line)
as any of the box boundaries, then the impact was detected.

## Code Results Discussion
The system is simulated for three seconds. In the simulation the jack is inside the box
and is not in contact with the box at �Ä, the start of the simulation. The jack then falls due to the
force of gravity on the jack and eventually collides with the bottom of the box. The box is also
experiencing a downward gravitational force and an external force. This external force causes
the box to oscillate in the vertical direction (y of box frame). When the jack collides with the 
box, both objects experience an instantaneous change velocity due to the forces that they
apply on each other. In total, 10 collisions are detected during the simulation.
To justify the accuracy of the dynamics of the simulated system various tests were
performed. First, the external force (the right-hand-side of the Forced Euler Lagrange
Equations) was set to 0. Then, the Hamiltonian was plotted.
Figure 2. Hamiltonian for the jack and box system with no external forces
The Hamiltonian of the system is nearly constant. The behavior of the Hamiltonian is
reasonable. The collisions in the system are elastic and there are no external forces, therefore,
conservation laws tell us that Figure 2 above is the qualitative result expected.
In addition, the trajectory of this system (with no external force) was plotted. This plot
was used to check the Lagrangian of the system. It is easy to predict the expected trajectory of
a rigid falling in gravity.
Figure 3. Box and jack system falling in gravity
The behavior for this plot was also as expected the jack and the box are falling with a
negative acceleration of 9.8 m/s.
Another reason why the behavior of the system is reasonable is it is evident from the
animation that when the jack collides with the box the jack exhibits a greater acceleration than
the box. This is as expected because the jack is significantly lighter than the box. 

## Resources 
The code for this simulation is available upon request. 