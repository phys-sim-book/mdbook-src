# Collision Constraints

in any physical simulation, preventing the interpenetration of objects is paramount for achieving plausible results. Position-Based Dynamics provides a particularly elegant and unified approach to this challenge. Collisions are not treated as a separate post-processing step involving impulses or penalty forces; instead, they are formulated as unilateral inequality constraints that are integrated directly into the core PBD solver loop. 

### Triangle Self-Collisions

For thin-shell objects like cloth or other deformable surfaces, a primary challenge is handling self-collision, where the object folds and interacts with itself. The most common scenario is a vertex penetrating a triangle from another part of the mesh. This interaction is modeled with a unilateral constraint involving all four participating particles.

Consider a vertex with position $\bm{q}$ and a triangle with vertices $\bm{p}_1, \bm{p}_2, \bm{p}_3$. A collision constraint can be formulated by defining a minimum separation distance, or thickness $h$, from the triangle plane. The unilateral constraint function is:
$$
{{numeq}}{eq:collision:vertex_triangle_constraint}

C(\bm{q}, \bm{p}_1, \bm{p}_2, \bm{p}_3) = (\bm{q} - \bm{p}_1) \cdot \bm{n} - h \ge 0
$$
where $\bm{n} = (\bm{p}_{2,1} \times \bm{p}_{3,1}) / |\bm{p}_{2,1} \times \bm{p}_{3,1}|$ is the unit normal of the triangle. In case of $C<0$, all four particles ($\bm{q}, \bm{p}_1, \bm{p}_2, \bm{p}_3$) are involved, and their positions are corrected according to their respective inverse masses to resolve the penetration while conserving momentum. It is also essential to check the barycentric coordinates of the vertex's projection onto the triangle plane to ensure the contact point lies within the triangle's boundaries before applying the correction.

### Particle-Environment Collisions

The simplest collision scenario involves a dynamic particle interacting with a static, immovable piece of geometry, such as a floor plane or a convex container. This geometry acts as a boundary for the simulation domain. For a particle at position $\bm{p}$ interacting with a static plane, the non-penetration condition can be formulated as a constraint on the particle's signed distance from the plane.

Let the plane be defined such that $\bm{n}^T\bm{p} - d_{\text{rest}} = 0$ for any point $\bm{p}$ on it, where $\bm{n}$ is the unit normal and $d_{\text{rest}}$ is an offset. A non-penetration constraint for a particle $\bm{p}$ is then written as an inequality:
$$
{{numeq}}{eq:collision:plane_constraint}

C(\bm{p}) = \bm{n}^T \bm{p} - d_{\text{rest}} \geq 0
$$
When a particle violates this constraint (i.e., $C(\bm{p}) < 0$), the PBD solver projects its position back to the surface. Since only one particle is dynamic, its inverse mass is effectively infinite compared to the static geometry, so it receives the full position correction required to satisfy $C(\bm{p})=0$. The correction simply moves the particle along the plane normal $\bm{n}$ to resolve the penetration. You can imagine how this can be simply extend to more complex shapes.

### Particle-Particle Collisions

For simulating systems composed of discrete elements, such as granular materials, we have to consider direct particle-particle collision. This constraint prevents any two particles from overlapping.

For two particles at positions $\bm{p}_i$ and $\bm{p}_j$ with corresponding radii $r_i$ and $r_j$, the non-penetration constraint is a simple inequality based on their center-to-center distance:
$$
{{numeq}}{eq:collision:particle_particle_constraint}

C(\bm{p}_i, \bm{p}_j) = |\bm{p}_i - \bm{p}_j| - (r_i + r_j) \ge 0
$$
Unlike the linear plane constraint, this function is non-linear. This can be solved similar to the stretching constraints considered in the previous section. The solver calculates a correction that pushes the two particles apart along the vector connecting their centers, distributing the correction based on their inverse masses to conserve linear momentum.

### Frictional Effects at the Position Level

Friction is a dissipative contact force that opposes relative tangential motion between surfaces. To be more robust we can incorporate friction directly into the position-level constraint solve. This method is applied after an interpenetration constraint between two particles, $i$ and $j$, has been resolved.

Let $\bm{p}_i$ and $\bm{p}_j$ be the particle positions at the start of the time step. Let $\bm{p}*_i$ and $\bm{p}*_j$ be the current candidate positions, which have already been corrected to resolve penetration. The core idea is to compute a frictional position correction that opposes the tangential component of the particles' relative displacement during the timestep.

First, we determine the relative displacement vector over the timestep, $\Delta\bm{p}_{\text{rel}}$:
$$
\Delta\bm{p}_{\text{rel}} = (\bm{p}*_i - \bm{p}_i) - (\bm{p}*_j - \bm{p}_j)
$$
Next, we find the tangential component of this displacement, $\Delta\bm{p}_t$, by projecting it onto the contact plane defined by the contact normal $\bm{n}$.
$$
{{numeq}}{eq:collision:tangential_displacement_corrected}
\Delta\bm{p}_t = \Delta\bm{p}_{\text{rel}} - (\Delta\bm{p}_{\text{rel}} \cdot \bm{n})\bm{n}
$$
The friction model determines the magnitude of the correction based on a comparison between the tangential displacement $|\Delta\bm{p}_t|$ and the static friction threshold, which is proportional to the penetration depth $d$ and the coefficient of static friction $\mu_s$.

A position correction vector, $\Delta\bm{p}_{\text{friction}}$, is calculated to oppose $\Delta\bm{p}_t$. This correction is then distributed between the two particles. The correction for particle $i$ is given by:
$$
{{numeq}}{eq:collision:friction_correction}

\Delta \bm{p}_i = -\frac{w_i}{w_i+w_j} \times
\begin{cases}
\Delta\bm{p}_t & \text{if } |\Delta\bm{p}_t| \le \mu_s d & \text{(Static Friction)} \\
\Delta\bm{p}_t \min\left(\frac{\mu_k d}{|\Delta\bm{p}_t|}, 1\right) & \text{otherwise} & \text{(Kinetic Friction)}
\end{cases}
$$
where $w_i = 1/m_i$ is the inverse mass and $\mu_k$ is the coefficient of kinetic friction. The negative sign is crucial, as friction must oppose the tangential displacement. In the static case, the correction fully cancels out the relative tangential movement. In the kinetic case, the correction is limited by the kinetic friction force (Coulomb's law). 