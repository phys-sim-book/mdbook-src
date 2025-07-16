## Position-Based Fluids: Density and Surface Constraints


The Position-Based Dynamics framework can be elegantly extended from solids to simulate incompressible fluids. The resulting method, known as Position-Based Fluids (PBF) {{#cite macklin2013position}}, replaces the complex pressure solves of traditional Smoothed Particle Hydrodynamics (SPH) {{#cite koschier2020smoothed}} with a set of geometric constraints. This approach inherits the stability and efficiency of PBD, allowing for large time steps suitable for real-time applications, while effectively enforcing the constant-density condition that characterizes incompressible flow. 

### The Per-Particle Density Constraint

The fundamental principle of PBF is to ensure that the density around each fluid particle remains constant. The density at a given particle's location is estimated using a kernel-based summation over its neighbors.

The density $\rho_i$ for a particle $i$ at position $\bm{p}_i$ is estimated as:
$$
{{numeq}}{eq:pbf:sph_density}

\rho_i = \sum_{j} m_j W(\bm{p}_i - \bm{p}_j, h)
$$
where the sum is over all neighboring particles $j$, $m_j$ is the mass of particle $j$, $h$ is the smoothing kernel radius, and $W$ is a radially symmetric smoothing kernel function. For simplicity, we can assume all particles have equal mass and absorb it into the density calculation.

The goal is to enforce that this estimated density $\rho_i$ matches a user-defined rest density $\rho_0$. This is formulated as a per-particle constraint function $C_i$.

> **{{def}}{def:pbf:density_constraint}[PBF Density Constraint]**
> For each particle $i$, the density constraint $C_i$ is defined as the scaled deviation from the rest density $\rho_0$:
$$
{{numeq}}{eq:pbf:constraint_func}
C_i(\bm{p}_1, ..., \bm{p}_N) = \frac{\rho_i}{\rho_0} - 1
$$
The constraint is satisfied when $C_i = 0$.

To solve this system of constraints, PBF computes a position correction $\Delta \bm{p}_i$ for each particle. Following the PBD methodology, we first compute the gradient of $C_i$ with respect to the position of a particle $k$:
$$
{{numeq}}{eq:pbf:constraint_gradient}
\nabla_{\bm{p}_k} C_i = \frac{1}{\rho_0} \sum_{j} m_j \nabla_{\bm{p}_k} W(\bm{p}_i - \bm{p}_j, h) =
\begin{cases}
\frac{1}{\rho_0} \sum_j m_j \nabla W(\bm{p}_i - \bm{p}_j, h) & \text{if } k=i \\
-\frac{m_k}{\rho_0} \nabla W(\bm{p}_i - \bm{p}_k, h) & \text{if } k \ne i
\end{cases}
$$
The standard PBD solver computes a single Lagrange multiplier $\lambda_i$ for each constraint $C_i$ using the formula $\lambda_i = -C_i / (\sum_k \|\nabla_{\bm{p}_k} C_i\|^2)$. The position correction for a particle $\Delta\bm{p}_i$ is then derived from the influence of its own constraint and the constraints of its neighbors. Due to the symmetry of the kernel gradient ($\nabla_{\bm{p}_i} W(\bm{p}_i - \bm{p}_j, h) = -\nabla_{\bm{p}_j} W(\bm{p}_i - \bm{p}_j, h)$), this results in a simple and efficient final update rule for the position correction of particle $i$:
$$
{{numeq}}{eq:pbf:position_correction}
\Delta \bm{p}_i = \frac{1}{\rho_0} \sum_j (\lambda_i + \lambda_j) \nabla W(\bm{p}_i - \bm{p}_j, h)
$$
Here, a different kernel, the Spiky kernel, is typically used for its non-vanishing gradient, which prevents particle clustering.

> **{{rem}}{rem:pbf:regularization}[Robustness and Constraint Softening]**
> A practical issue arises when a particle has few neighbors, as the denominator $\sum_k \|\nabla_{\bm{p}_k} C_i\|^2$ can approach zero, leading to large, unstable position corrections. To prevent this, a small relaxation parameter $\varepsilon$ is added to the denominator, softening the constraint. This is known as Constraint Force Mixing (CFM) {{#cite smith2005open}}. Then:
$$
{{numeq}}{eq:pbf:lambda_cfm}
\lambda_i = - \frac{C_i(\bm{p}_1, ..., \bm{p}_N)}{\sum_k \|\nabla_{\bm{p}_k} C_i\|^2 + \varepsilon}
$$

### Correcting for Tensile Instability

A well-known artifact in SPH-based fluid simulations is the "tensile instability," where particles near a free surface clump together due to an inability to satisfy the rest density. PBF offers a direct solution by incorporating an artificial pressure term.

Specifically, an artificial pressure term, $s_{\text{corr}}$, is introduced to create a short-range repulsive force between nearby particles. This term is a function of  $W$:
$$
{{numeq}}{eq:pbf:scorr}
s_{\text{corr}} = -k \left( \frac{W(\bm{p}_i - \bm{p}_j, h)}{W(\Delta\bm{q}, h)} \right)^n
$$
where $k$ and $n$ are small positive constants (e.g., $k=0.1, n=4$), and $\Delta\bm{q}$ is a vector representing a small distance within the kernel radius (e.g., $\|\Delta\bm{q}\| = 0.3h$). This term is only applied to push particles apart and is incorporated directly into the position correction calculation from Equation {{eqref:eq:pbf:position_correction}}:
$$
{{numeq}}{eq:pbf:scorr_correction}
\Delta \bm{p}_i = \frac{1}{\rho_0} \sum_j (\lambda_i + \lambda_j + s_{\text{corr}}) \nabla W(\bm{p}_i - \bm{p}_j, h)
$$
This prevents clumping, and implicitly creates a surface tension-like effect at the fluid's boundary.

### Velocity-Level Corrections: Viscosity and Vorticity

While the primary constraints in PBF operate on positions, velocity-level updates are still necessary to model phenomena like viscosity and to reintroduce lost energy. These are applied as a post-process after the main PBD solver loop has updated positions and velocities.

**XSPH Viscosity:** To impart a more coherent, liquid-like motion and reduce particle intermixing, XSPH viscosity is applied. It adjusts each particle's velocity to be closer to the average velocity of its neighbors:
$$
{{numeq}}{eq:pbf:xsph_viscosity}
\bm{v}_i^{\text{new}} = \bm{v}_i + c \sum_j (\bm{v}_j - \bm{v}_i) W(\bm{p}_i - \bm{p}_j, h)
$$
where $c$ is a positive viscosity coefficient, typically a small value like $0.01$.

**Vorticity Confinement:** The iterative nature of PBD can introduce numerical damping, causing the fluid to lose rotational energy and appear overly viscous. Vorticity confinement can be used to counteract this by calculating the vorticity (curl of the velocity field) $\bm{\omega}_i = \nabla \times \bm{v}_i$ at each particle and applying a corrective force $\bm{f}_{\text{vorticity}} = \varepsilon (\bm{N} \times \bm{\omega}_i)$ to reintroduce small-scale rotational details. This force is then integrated to update particle velocities.
