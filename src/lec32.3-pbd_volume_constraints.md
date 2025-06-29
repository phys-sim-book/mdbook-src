# Volume Conservation Constraints

The simulation of incompressible or nearly incompressible materials is critical for many applications in computer graphics. In a force-based framework, incompressibility typically requires solving a complex Poisson equation for pressure. PBD offers a more direct and often simpler approach by enforcing volume or density conservation through geometric constraints. This section will explore several such constraints, beginning with those defined on discrete mesh elements like tetrahedra and extending to global volume constraints for closed surfaces.

### Tetrahedral Volume and Triangle Area Constraints

For volumetric objects discretized into a tetrahedral mesh, incompressibility can be approximated by constraining the volume of each individual tetrahedron to remain at its rest volume.

> **{{def}}{def:volume:tetrahedral}[Tetrahedral Volume Constraint]**
> Given a tetrahedron defined by four vertices with positions $\bm{p}_1, \bm{p}_2, \bm{p}_3, \bm{p}_4$ and a rest volume $V_0$, the volume constraint function $C$ is defined as:
$$
{{numeq}}{eq:volume:tetra_constraint}
C(\bm{p}_1, \bm{p}_2, \bm{p}_3, \bm{p}_4) = \frac{1}{6} \left( (\bm{p}_2 - \bm{p}_1) \times (\bm{p}_3 - \bm{p}_1) \right) \cdot (\bm{p}_4 - \bm{p}_1) - V_0
$$
> The constraint is satisfied when $C=0$. The gradients of this function with respect to the four vertex positions are:
 $$
 \begin{aligned}
 \nabla_{\bm{p}_1} C &= \frac{1}{6} \left( (\bm{p}_3 - \bm{p}_2) \times (\bm{p}_4 - \bm{p}_2) \right) \\
 \nabla_{\bm{p}_2} C &= \frac{1}{6} \left( (\bm{p}_4 - \bm{p}_3) \times (\bm{p}_1 - \bm{p}_3) \right) \\
 \nabla_{\bm{p}_3} C &= \frac{1}{6} \left( (\bm{p}_1 - \bm{p}_4) \times (\bm{p}_2 - \bm{p}_4) \right) \\
 \nabla_{\bm{p}_4} C &= \frac{1}{6} \left( (\bm{p}_2 - \bm{p}_1) \times (\bm{p}_3 - \bm{p}_1) \right)
 \end{aligned}
 $$
> These gradients—vectors proportional to the areas of the opposing faces—are used to calculate position corrections for the four vertices. An analogous constraint can be defined for the area of a triangle in 2D or 3D simulations.

### Global Volume for Closed Meshes

For hollow objects represented by a closed, watertight triangle mesh (such as an inflatable character), it is often desirable to control the total enclosed volume. This can be achieved with a single global constraint that acts on all vertices of the mesh. The total volume $V$ of a closed mesh can be calculated using the Divergence Theorem, which discretizes to a sum over all triangles. The volume contributed by each triangle $i$ with vertices $(\bm{p}_{t_1}^i, \bm{p}_{t_2}^i, \bm{p}_{t_3}^i)$ is the signed volume of the tetrahedron formed by the triangle and the origin:

$$
V = \sum_{i}^{\text{N\_triangles}} \frac{1}{6} \left( \bm{p}_{t_1}^i \times \bm{p}_{t_2}^i \right) \cdot \bm{p}_{t_3}^i
$$

The global volume constraint is then formulated to maintain this volume at a target value, which is typically the rest volume $V_0$ scaled by a pressure factor $k_{\text{pressure}}$:
$$
{{numeq}}{eq:volume:global_constraint}

C(\bm{p}_1, ..., \bm{p}_N) = \left( \sum_{i} \frac{1}{6} (\bm{p}_{t_1^i} \times \bm{p}_{t_2^i}) \cdot \bm{p}_{t_3^i} \right) - k_{\text{pressure}} V_0
$$
The gradient of this constraint with respect to a vertex position $\bm{p}_j$ is the sum of the gradients from all triangles adjacent to that vertex. This results in a correction that redistributes the volume error across the entire surface.
<!-- 
### Density Constraints for Fluids

The concept of volume conservation can be extended to the particle level to simulate incompressible fluids. In the Position-Based Fluids (PBF) method of Macklin and Müller [MM13], the fluid is represented by a set of particles, and incompressibility is enforced by constraining the local density around each particle.

The density $\rho_i$ at a particle $i$ is estimated using a kernel-based summation over its neighbors, a technique borrowed from Smoothed Particle Hydrodynamics (SPH):
{{numeq}}{eq:volume:sph_density}
$$
\rho_i = \sum_{j} m_j W(\bm{p}_i - \bm{p}_j, h)
$$
where $m_j$ is the mass of a neighboring particle $j$, $W$ is a smoothing kernel (e.g., the poly6 kernel), and $h$ is the smoothing radius.

The per-particle density constraint aims to keep this estimated density equal to a specified rest density $\rho_0$:
{{numeq}}{eq:volume:density_constraint}
$$
C_i(\bm{p}_1, ..., \bm{p}_N) = \frac{\rho_i}{\rho_0} - 1 = 0
$$
The gradient of this constraint with respect to a particle's position $\bm{p}_k$ is:
{{numeq}}{eq:volume:density_gradient}
$$
\nabla_{\bm{p}_k} C_i = \frac{1}{\rho_0} \sum_j m_j \nabla_{\bm{p}_k} W(\bm{p}_i - \bm{p}_j, h) =
\begin{cases}
\frac{1}{\rho_0} \sum_j m_j \nabla W(\bm{p}_i - \bm{p}_j, h) & \text{if } k=i \\
-\frac{m_k}{\rho_0} \nabla W(\bm{p}_i - \bm{p}_k, h) & \text{if } k \ne i
\end{cases}
$$
During the PBD solver step, each particle calculates a position correction $\Delta \bm{p}_i$ to resolve its own density error. This is done by iteratively solving for the Lagrange multiplier $\lambda_i$ associated with $C_i$ and applying corrections. The symmetry of the kernel gradient allows for an efficient computation where the total position correction for a particle is a sum of pairwise interactions with its neighbors. This elegant method allows PBD to simulate fluid phenomena, such as splashing and wave propagation, with unconditional stability. -->
