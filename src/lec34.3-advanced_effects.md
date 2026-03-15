## Advanced Fluid Effects: Viscosity and Vorticity

While the primary constraints in PBF operate on positions, velocity-level updates are still necessary to model phenomena like viscosity and to reintroduce lost energy. These are applied as a post-process after the main PBD solver loop has updated positions and velocities.

**XSPH Viscosity:** To impart a more coherent, liquid-like motion and reduce particle intermixing, XSPH viscosity is applied. It adjusts each particle's velocity to be closer to the average velocity of its neighbors:
$$
{{numeq}}{eq:pbf:xsph_viscosity}
\bm{v}_i^{\text{new}} = \bm{v}_i + c \sum_j (\bm{v}_j - \bm{v}_i) W(\bm{p}_i - \bm{p}_j, h)
$$
where $c$ is a positive viscosity coefficient, typically a small value like $0.01$.

**Vorticity Confinement:** The iterative nature of PBD can introduce numerical damping, causing the fluid to lose rotational energy and appear overly viscous. Vorticity confinement can be used to counteract this by calculating the vorticity (curl of the velocity field) $\bm{\omega}_i = \nabla \times \bm{v}_i$ at each particle and applying a corrective force $\bm{f}_{\text{vorticity}} = \varepsilon (\bm{N} \times \bm{\omega}_i)$. Here, $\bm{N}$ is the normalized gradient of the vorticity's magnitude, $\bm{N} = \nabla|\bm{\omega}|/\|\nabla|\bm{\omega}|\|$, which points toward areas of increasing rotation. This force reintroduces small-scale rotational details.

### Implementation: Advanced Fluid Effects

Both XSPH viscosity and vorticity confinement are implemented as post-processing steps after the main PBD solver to enhance fluid realism:

**XSPH Viscosity Implementation**

XSPH viscosity smooths particle velocities by averaging with neighboring particles:

```python
@ti.kernel
def apply_xsph_viscosity():
    """XSPH viscosity implementation - Equation {{eqref:eq:pbf:xsph_viscosity}}"""
    for i in positions:
        vel_i = velocities[i]
        delta_v = ti.Vector([0.0, 0.0, 0.0])
        for j in range(particle_num_neighbors[i]):
            p_j = particle_neighbors[i, j]
            pos_ji = positions[i] - positions[p_j]
            vel_ij = velocities[p_j] - vel_i
            w_ij = poly6_value(pos_ji.norm(), h)
            delta_v += vel_ij * w_ij
        velocity_deltas_xsph[i] = xsph_viscosity_c * delta_v

    for i in positions:
        velocities[i] += velocity_deltas_xsph[i]
```

This creates more coherent, liquid-like motion by reducing velocity differences between neighboring particles, effectively simulating viscous behavior.

**Vorticity Confinement Implementation**

Vorticity confinement reintroduces rotational energy lost during the iterative constraint solving process:

```python
@ti.kernel
def calculate_vorticity():
    """Calculate vorticity field: ω_i = ∇ × v_i"""
    for i in positions:
        vort_i = ti.Vector([0.0, 0.0, 0.0])
        for j in range(particle_num_neighbors[i]):
            p_j = particle_neighbors[i, j]
            pos_ji = positions[i] - positions[p_j]
            vel_ij = velocities[p_j] - velocities[i]
            vort_i += vel_ij.cross(spiky_gradient(pos_ji, h))
        vorticity[i] = vort_i
```

First, the vorticity field is computed by taking the curl of the velocity field using the cross product of relative velocities and kernel gradients.

```python
@ti.kernel
def apply_vorticity_confinement():
    """Apply vorticity confinement force: f_vorticity = ε(N × ω_i)"""
    for i in positions:
        eta = ti.Vector([0.0, 0.0, 0.0])
        vort_i_norm = vorticity[i].norm()
        for j in range(particle_num_neighbors[i]):
            p_j = particle_neighbors[i, j]
            pos_ji = positions[i] - positions[p_j]
            vort_j_norm = vorticity[p_j].norm()
            eta += (vort_j_norm - vort_i_norm) * spiky_gradient(pos_ji, h)
        
        eta_norm = eta.norm()
        if eta_norm > epsilon:
            N = eta / eta_norm
            vorticity_force[i] = vorticity_epsilon * N.cross(vorticity[i])
        else:
            vorticity_force[i] = ti.Vector([0.0, 0.0, 0.0])

    for i in velocities:
        velocities[i] += vorticity_force[i] * time_delta
```

The confinement force is computed by finding the gradient of vorticity magnitude and applying a corrective force perpendicular to both the vorticity vector and its gradient, reintroducing small-scale rotational details.
