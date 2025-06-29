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
