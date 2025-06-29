# Constraint Solver
The heart of the PBD algorithm is the constraint solver, which iteratively projects the predicted particle positions to satisfy the defined constraints. Since this projection must be done in a physically plausible manner, ideally conserving the system's total linear and angular momentum for internal constraints. We employ a Non-Linear Gauss-Seidel Solver.

## Constraint Projection: Momentum Conservation 
We make sure that for any internal constraint, the correction step should not introduce fictitious external forces, often referred to as "ghost forces." This is achieved by ensuring that the net change in linear and angular momentum is zero. Let $\Delta\bm{p}_i$ be the position correction for particle $i$. Linear momentum is conserved if the center of mass remains unchanged by the correction:

$$
\sum_i m_i \Delta\bm{p}_i = \bm{0}
$$

Angular momentum is conserved if the net torque produced by the corrections is zero with respect to a common center of rotation $\bm{c}$:

$$
\sum_i (\bm{p}_i - \bm{c}) \times (m_i \Delta\bm{p}_i) = \bm{0}
$$

> A key insight is that if the correction vector for the concatenated particle positions, $\Delta\bm{p} = [\Delta\bm{p}_1^T, \dots, \Delta\bm{p}_n^T]^T$, is chosen to be parallel to the constraint gradient $\nabla_{\bm{p}}C$, that is:
> $$\Delta p ^\top \nabla_{\bm{p}}C = 0,a$$
> then for many common internal constraints (which are independent of rigid body transformations), both momenta are automatically conserved when particle masses are equal.

As such the correction for each particle will be assumed to be parallel to the constraint gradient. This choice is supported by the fact that since the gradient points in the direction of the steepest ascent of the constraint function, and thus moving in the opposite direction is the most direct way to reduce the constraint error. 


## Position Correction (General Constraints)
 
Consider a single constraint $C(\bm{p}_1, \dots, \bm{p}_n) = 0$ involving $n$ particles. Given a set of predicted positions $\bm{p}$ that violate this constraint (i.e., $C(\bm{p}) \neq 0$), we seek a correction $\Delta\bm{p}$ such that $C(\bm{p} + \Delta\bm{p}) = 0$. To make this tractable, we linearize the constraint function using a first-order Taylor expansion around the current positions $\bm{p}$:
$$
\begin{align*}
{{numeq}}{eq:pbd:linearized_constraint}
C(\bm{p} + \Delta\bm{p}) &= C(\bm{p}) + \nabla_{\bm{p}} C(\bm{p})^\top \Delta\bm{p} + \mathcal{O}(|\Delta p|^2) \succ 0, \\
C(\bm{p} + \Delta\bm{p}) &\approx C(\bm{p}) + \nabla_{\bm{p}} C(\bm{p})^\top \Delta\bm{p} \succ 0.
\end{align*}
$$

As established above, we restrict the correction to be in the direction of the constraint gradient. This allows us to define the correction for a single particle $i$ in terms of a single unknown scalar $\lambda$, which acts as a Lagrange multiplier:
$$
{{numeq}}{eq:solver:correction_form}

\Delta\bm{p}_i = - \lambda \, w_i \, \nabla_{\bm{p}_i} C(\bm{p})
$$
where $w_i = 1/m_i$ is the inverse mass, ensuring that lighter particles are displaced more. The negative sign is a convention to align $\lambda$ with the concept of a repulsive force for a positive constraint violation. In vector form for all involved particles, this is $\Delta\bm{p} = -\lambda \mathbf{M}^{-1} \nabla_{\bm{p}}C$, where $\mathbf{M}$ is the diagonal mass matrix.

To find the value of $\lambda$, we substitute the correction from Equation {{eqref:eq:solver:correction_form}} back into the linearized constraint, Equation {{eqref:eq:pbd:linearized_constraint}}. For an equality constraint, this becomes:
$$
C(\bm{p}) + \nabla_{\bm{p}} C(\bm{p})^\top (-\lambda \mathbf{M}^{-1} \nabla_{\bm{p}} C(\bm{p})) = 0
$$
Solving for the Lagrange multiplier $\lambda$ yields:
$$
{{numeq}}{eq:solver:lambda}

\lambda = \frac{C(\bm{p})}{\sum_{j=1}^{n} w_j |\nabla_{\bm{p}_j} C(\bm{p})|^2}
$$
With $\lambda$ determined, the position correction $\Delta\bm{p}_i$ for each particle is fully defined. We can also write the full correction in a single expression by substituting Equation {{eqref:eq:solver:lambda}} into Equation {{eqref:eq:solver:correction_form}}:
$$
{{numeq}}{eq:pbd:final_correction}

\Delta\bm{p}_i = - \frac{C(\bm{p}_1, \dots, \bm{p}_n)}{\sum_{j=1}^{n} w_j |\nabla_{\bm{p}_j}C|^2} w_i \nabla_{\bm{p}_i}C(\bm{p})
$$
This equation is the cornerstone of the PBD solver. It provides a direct, computationally efficient method to calculate the position corrections required to satisfy a single linearized constraint while respecting particle masses. This projection is solved multiple times for each constraint within a single time step. It is important to note that for constraints that are linear along their gradient, such as a simple distance constraint, this linearization is exact, and the constraint can be satisfied in a single step.



> **{{exp}}{exp:solver:distance_constraint}[Distance Constraint]**
> Let us consider one of the most fundamental constraints: the distance constraint, which enforces a fixed separation $d$ between two particles, $\bm{p}_1$ and $\bm{p}_2$. This is a common building block used to model stretching resistance in springs, the edges of a cloth mesh, or rigid links between objects.
>
> The constraint function is defined as the difference between the current distance and the desired rest distance $d$:
$$
  C(\bm{p}_1, \bm{p}_2) = |\bm{p}_1 - \bm{p}_2| - d
$$
> This function directly measures the error that needs to be corrected. While the general projection method from Equation {{eqref:eq:pbd:final_correction}} can be applied, for this specific and common case, the result simplifies to a very intuitive form. The final position corrections for each particle are given directly by:
$$
 \begin{align*}
 \Delta\bm{p}_1 &= - \frac{w_1}{w_1 + w_2} (|\bm{p}_1 - \bm{p}_2| - d) \frac{\bm{p}_1 - \bm{p}_2}{|\bm{p}_1 - \bm{p}_2|} && \\
 \Delta\bm{p}_2 &= + \frac{w_2}{w_1 + w_2} (|\bm{p}_1 - \bm{p}_2| - d) \frac{\bm{p}_1 - \bm{p}_2}{|\bm{p}_1 - \bm{p}_2|} && 
 \end{align*}
 $$
>  The term $(|\bm{p}_1 - \bm{p}_2| - d)$ represents the total correction magnitude needed along the vector connecting the particles. This total correction is then distributed between the two particles based on their inverse mass ratio, $w_i / (w_1 + w_2)$. The correction is applied along the unit vector $(\bm{p}_1 - \bm{p}_2)/|\bm{p}_1 - \bm{p}_2|$, moving the particles towards each other if they are too far apart ($C > 0$) and away from each other if they are too close ($C < 0$). This formulation directly satisfies the conservation of linear momentum!