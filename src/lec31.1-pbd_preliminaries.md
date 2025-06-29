# Preliminaries

To fully appreciate the PBD approach, it is essential to first understand the classical formulation of Lagrangian dynamics it seeks to replace. The state of a dynamic system composed of $N$ particles is described by their individual mass $m_i$, position $\bm{p}_i \in \mathbb{R}^3$, and velocity $\bm{v}_i \in \mathbb{R}^3$. The evolution of this system is governed by a pair of first-order ordinary differential equations derived from Newton's second law:
$$
\dot{\bm{v}}_i = \frac{1}{m_i} \bm{f}_i
{{numeq}}{eq:lagrangian:particle_motion}
$$
$$
\dot{\bm{p}}_i = \bm{v}_i
{{numeq}}{eq:lagrangian:particle_kinematic}

$$
where $\bm{f}_i$ is the sum of all forces on particle $i$.

Rigid bodies require additional attributes to describe their rotational state: an orientation quaternion $\bm{q}_i \in \mathbb{H}$, an angular velocity $\bm{\omega}_i \in \mathbb{R}^3$, and an inertia tensor $\bm{I}_i \in \mathbb{R}^{3 \times 3}$. The rotational motion is then described by the Newton-Euler equations:
$$
\dot{\bm{\omega}}_i = \bm{I}_i^{-1} \left( \bm{\tau}_i - \left( \bm{\omega}_i \times (\bm{I}_i \bm{\omega}_i) \right) \right)
{{numeq}}{eq:lagrangian:rigid_body_motion}

$$
$$
\dot{\bm{q}}_i = \frac{1}{2} \hat{\bm{\omega}}_i \bm{q}_i
{{numeq}}{eq:lagrangian:rigid_body_kinematic}

$$
where $\bm{\tau}_i$ is the net torque and $\hat{\bm{\omega}}_i$ is the quaternion $[0, \bm{\omega}_i]$.

To simulate this evolution, these continuous equations are discretized using a numerical integrator. The Symplectic Euler method updates the velocity first, then uses this new velocity to update the position, improving stability over standard explicit Euler. For a particle, with time step $\Delta t$, the update is:
$$
\bm{v}_i(t_0 + \Delta t) = \bm{v}_i(t_0) + \Delta t \frac{1}{m_i} \bm{f}_i(t_0)
{{numeq}}{eq:lagrangian:symplectic_euler_v}

$$
$$
\bm{p}_i(t_0 + \Delta t) = \bm{x}_i(t_0) + \Delta t \bm{v}_i(t_0 + \Delta t)
{{numeq}}{eq:lagrangian:symplectic_euler_x}

$$
This procedure is applied analogously for rigid body states.

> **{{rem}}{rem:lagrangian:quaternion_normalization}[Quaternion Normalization]**
> Due to numerical integration error, the quaternion $\bm{q}_i$ may drift from unit length. It is essential to re-normalize the quaternion after each integration step to maintain a valid rotational state.

Finally, interactions and physical limits are modeled using **holonomic constraints**, which depend only on positions and orientations, but not velocities! Constraints are kinematic restrictions in the form of equations and
inequalities that constrain the relative motion of bodies. An **equality (bilateral)** constraint takes the form $C_j(\bm{p}, \bm{q}) = 0$, while an **inequality (unilateral)** constraint is $C_j(\bm{p}, \bm{q}) \ge 0$. In classical dynamics, these are satisfied by computing constraint forces and adding them to $\bm{f}_i$ in Equation {{eqref:eq:lagrangian:particle_motion}}. It is this specific mechanism for handling constraints that PBD fundamentally changes.