# Multi-Particle Animation with Social Forces
## Overview
This document will provide an analysis of simulating multi-particle systems using social force models within a gradient descent optimization framework. This simulation builds upon the basic principle of singular particle motion following a downward gradient towards a goal, but includes "social" forces between the particles adding another level of complexity.

This simulation will tackle two main types of social forces: Isotropic and Anisotropic. **Isotropic** models, which rely purely on Euclidean distance, and **Anisotropic** models, which incorporate velocity-dependent directional bias. The analysis demonstrates that while isotropic forces are sufficient for simple collision avoidance, they often lead to symmetric deadlocks and unrealistic oscillations in crowded scenarios. Anisotropic forces, by mimicking the human tendency to react primarily to frontal obstacles, break this symmetry and facilitate smoother collective flow.

## Cost Function Formulation
The the particle animation are simply using the gradient-descent algorithm to reach their goal:
$$
\begin{align}
\hat{\bf  \Phi} = \arg_{{{\bf  \Phi}}}\min C\left({\bf  \Phi}\right),
%\label{simple_motion_min}
\end{align}
$$

For a system of N particles with random positions
$$
\begin{align}
C_i (x_i)=C_{goal}(x_i) +C_{walls}(x_i)+\sum_{i\neq j}C_{social}(x_i,x_j)
%\label{cost_function}
\end{align}
$$
where:
- $C_{goal}$: Attracts the particle to its target destination.
- $C_{walls}$: Repels the particle from static environment boundaries.
- $C_{social}$: Repels the particle from other dynamic agents $j$.

## Gradient Descent Motion
This simulation uses a more fluid approach where velocity is directly proportional to the negative gradient of the cost function (a.k.a moving downward). 
The equation bellow is the general gradient decent algorithms with time step $k$ is:
$$
x_{i, (k+1)}  =  x_{i,k}  - α ∇x_iC_i
$$
Here, $\alpha$ is the step size (learning rate).

### Calculating the Derivative
For this simulation, we primarily use a modified version than the base definition to calculate the derivative.
From the definition derivative:
$$
\frac{df}{dx} = \lim_{ \Delta x \to 0 } \frac{ f(x + \Delta x) - f(x)}{\Delta x}
$$
We can modify the definition include the negative variation resulting in:
 $$
\frac{df}{dx} = \lim_{ \Delta x \to 0 } \frac{ f(x + \Delta x) - f(x - \Delta x)}{2\Delta x}
$$
Which we can use the central approximation of the derivative to get the final equation:
 $$
\frac{df}{dx} \approx \frac{ f(x + \Delta x) - f(x - \Delta x)}{2\Delta x}
$$
This equation shows more stability with the particles since it also includes derivatives of both directions instead of one.
*Note: This option can be changed in the program but for the purpose of this document we will only be using this equation*

## Social Force Models
### Isotropic Models
Isotropic forces assume that the repulsion between two particles depends **only** on the Euclidean distance $d_{ij} = ||\mathbf{x}_i - \mathbf{x}_j||$. The interaction is symmetric regardless of the particles' relative velocities.

#### Quadratic Repulsion (Soft Personal Space)
Defines a specific radius $R$ of personal space. No force is exerted beyond this radius.
$$
C_{social}(i, j)  = \begin{cases}
    0.5(R-d_{i,j})^2 & \text{if } d_{i,j} < R \\
    0 & \text{else }
\end{cases}
$$