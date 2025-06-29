# Stiffness and Damping in Constraint Solving


## Stiffness
Material stiffness is controlled by a parameter $k \in [0, 1]$; $k$ controls how strongly a constraint is enforced. The most straightforward way to incorporate stiffness is to scale the calculated correction by $k$: $\Delta\bm{p}_i \leftarrow k \cdot \Delta\bm{p}_i$. However, this makes the effective stiffness dependent on the number of solver iterations, $n_s$. If a constraint is projected $n_s$ times, the remaining error after the projections would be proportional to $(1-k)^{n_s}$. This makes the effective stiffness dependent on the number of solver iterations, which is undesirable. A more robust formulation that decouples stiffness from the iteration count is:
$$
{{numeq}}{eq:pbd:stiffness}
k' = 1 - (1 - k)^{1/n_s}
$$
By scaling the correction by $k'$ instead of $k$, the remaining error after $n_s$ iterations becomes $(1-k')^{n_s} = 1-k$, which is independent of $n_s$. This allows artists to tune the material stiffness $k$ without worrying about how many solver iterations are being used.




## Damping
Although the PBD method is inherently stable, simulations can exhibit excessive oscillations or kinetic energy gain, particularly with stiff constraints. The quality of dynamic simulations can generally be improved by the incorporation of an appropriate damping scheme. It can improve the stability by reducing temporal jittering of the point positions of an object. It allows for larger time steps which increases the performance of a dynamic simulation.

A naive damping of all velocities can undesirably affect the global motion of an object, slowing down its overall translation and rotation. A more sophisticated approach is to damp only the relative motions of particles while preserving the total linear and angular momentum of the system.




> **{{met}}{met:pbd:damping}[Momentum-Conserving Damping]**
>
> 1.  **Compute Center of Mass and Velocity:**
>     $\bm{p}_{cm} = \frac{\sum_i m_i \bm{p}_i}{\sum_i m_i}$,
>     $\bm{v}_{cm} = \frac{\sum_i m_i \bm{v}_i}{\sum_i m_i}$
>
> 2.  **Compute Angular Momentum and Inertia Tensor:** Let $\bm{r}_i = \bm{x}_i - \bm{p}_{cm}$.
> 
>     $\bm{L} = \sum_i \bm{r}_i \times (m_i \bm{v}_i)$
>
>     $\bm{I} = \sum_i m_i ((\bm{r}_i \cdot \bm{r}_i)\mathbf{Id} - \bm{r}_i \otimes \bm{r}_i)$
>
> 3.  **Compute Angular Velocity:**
>     $\bm{\omega} = \bm{I}^{-1}\bm{L}$
>
> 4.  **Apply Damping:** For each particle $i$:
>
>     a.  Calculate the velocity deviation from rigid body motion:
>         $\Delta\bm{v}_i = (\bm{v}_{cm} + \bm{\omega} \times \bm{r}_i) - \bm{v}_i$
>
>     b.  Apply damping to the deviation:
>         $\bm{v}_i \leftarrow \bm{v}_i + k_{damping} \Delta\bm{v}_i$
>

This method effectively isolates the non-rigid components of motion and damps only them. In the extreme case where $k_{damping} = 1$, all relative motion is eliminated, and the object behaves as a perfect rigid body.


## Extended Position Based Dynamics (XPBD)

While the stiffness formulation in Equation {{eqref:eq:pbd:stiffness}} decouples the effective stiffness from the solver iteration count, a fundamental limitation of standard PBD remains: the resulting material stiffness is still dependent on the simulation time step $\Delta t$. An extension known as **Extended Position Based Dynamics (XPBD)** addresses this issue, enabling true parameter-independent stiffness.

XPBD is derived from a compliant constraint formulation and introduces the concept of compliance as the inverse of stiffness.

> **{{def}}{def:pbd:compliance}[Compliance]**
> Compliance, $\alpha \ge 0$, is the inverse of a material's stiffness $k$, with $\alpha = 1/k$. It describes a material's propensity to deform under load. In XPBD, a compliance of $\alpha = 0$ corresponds to an infinitely stiff, or hard, constraint.

The core idea of XPBD is to treat the Lagrange multiplier $\lambda$ not as a temporary value recalculated in each iteration, but as a physical quantity representing the accumulated impulse that is incrementally updated. In each solver iteration, we calculate an impulse increment, $\Delta\lambda$, and add it to the total impulse $\lambda$ for that constraint. The formula for this increment modifies Equation {{eqref:eq:solver:lambda}} as follows:
$$
{{numeq}}{eq:pbd:xpbd_delta_lambda}

\Delta\lambda = \frac{-C(\bm{p}) - \hat{\alpha} \lambda}{\sum_{j} w_j |\nabla_{\bm{p}_j} C(\bm{p})|^2 + \hat{\alpha}}
$$
Here, $\lambda$ is the total accumulated Lagrange multiplier for the constraint from previous iterations within the current time step. The term $\hat{\alpha}$ is the time-step scaled compliance, defined as:
$$
\hat{\alpha} = \frac{\alpha}{(\Delta t)^2}
$$
This scaling ensures that the compliance parameter $\alpha$ has physically consistent units within the dynamic system. After computing $\Delta\lambda$, the solver updates both the particle positions and the accumulated Lagrange multiplier for that constraint:
$$
\Delta\bm{p}_i = - \Delta\lambda \, w_i \, \nabla_{\bm{p}_i} C(\bm{p}) \qquad \text{and} \qquad \lambda \leftarrow \lambda + \Delta\lambda
$$

> **{{rem}}{rem:pbd:xpbd_benefits}[Interpretation of XPBD]**
> The $+\hat{\alpha}$ term in the denominator of Equation {{eqref:eq:pbd:xpbd_delta_lambda}} acts to limit the magnitude of the corrective impulse $\Delta\lambda$. As compliance $\alpha$ increases (i.e., the material becomes softer), $\hat{\alpha}$ grows, thus reducing the impulse applied per iteration. In the case of zero compliance ($\alpha=0$), the $\hat{\alpha}$ terms vanish, and the formula for $\Delta\lambda$ reduces to the standard PBD formulation in Equation {{eqref:eq:solver:lambda}}.


The primary benefit of XPBD is **not faster convergence, but convergence to a physically consistent state that correctly reflects the user-defined compliance $\alpha$**, independent of the time step or iteration count. If the solver is terminated early, the system exhibits the desired softness rather than an artificial, uncontrolled compliance. This makes material behavior predictable and robust.