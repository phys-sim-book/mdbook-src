## Stretching Resistance via Distance Constraints

The primary characteristic of most textiles is their high resistance to stretching. In PBD, this property is enforced by constraining the distance between connected particles to remain close to its initial, or rest, distance. This is one of the simplest yet most crucial constraints in the PBD ecosystem.

### Theory: Distance Constraint Formulation

Consider two particles, $i=1, 2$, with positions $\bm{p}_1$ and $\bm{p}_2$, masses $m_1$ and $m_2$, and a rest distance $d$ between them. The stretching constraint function $C$ is defined as the difference between the current distance and the rest distance:

> **{{def}}{def:cloth:stretching_constraint}[Stretching Constraint]**
The stretching constraint function $C$ for two particles is:
$$
{{numeq}}{eq:cloth:stretch_constraint}
C(\bm{p}_1, \bm{p}_2) = \|\bm{p}_1 - \bm{p}_2\| - d
$$
The goal is to find corrections $\Delta\bm{p}_1$ and $\Delta\bm{p}_2$ such that $C(\bm{p}_1+\Delta\bm{p}_1, \bm{p}_2+\Delta\bm{p}_2) = 0$.

The gradients of the constraint function with respect to the particle positions are:
$$
\nabla_{\bm{p}_1} C = \frac{\bm{p}_1 - \bm{p}_2}{\|\bm{p}_1 - \bm{p}_2\|} = \bm{n} \quad \text{and} \quad \nabla_{\bm{p}_2} C = -\frac{\bm{p}_1 - \bm{p}_2}{\|\bm{p}_1 - \bm{p}_2\|} = -\bm{n}
$$
where $\bm{n}$ is the unit vector along the axis connecting the two particles.

Following the general PBD projection formula, the scalar Lagrange multiplier $\lambda$ is computed as:
$$
{{numeq}}{eq:cloth:lambda_stretch}
\lambda = \frac{C(\bm{p}_1, \bm{p}_2)}{w_1\|\nabla_{\bm{p}_1} C\|^2 + w_2\|\nabla_{\bm{p}_2} C\|^2} = \frac{\|\bm{p}_1 - \bm{p}_2\| - d}{w_1 + w_2}
$$
where $w_i = 1/m_i$ is the inverse mass of particle $i$.

The position corrections are then found by moving the particles along their respective gradient directions, scaled by their inverse mass and $\lambda$:
$$
{{numeq}}{eq:cloth:position_correction}
\Delta\bm{p}_1 = - \frac{w_1}{w_1+w_2}(\|\bm{p}_1 - \bm{p}_2\| - d)\bm{n} \\
\Delta\bm{p}_2 = + \frac{w_2}{w_1+w_2}(\|\bm{p}_1 - \bm{p}_2\| - d)\bm{n}
$$

These corrections, when applied, will move the particles to exactly satisfy the rest length. The total correction is distributed between the particles based on their inverse mass, ensuring that lighter particles move more than heavier ones and that linear momentum is conserved.

To achieve different levels of elasticity, a stiffness parameter $k \in [0, 1]$ can be introduced by scaling the corrections $\Delta\bm{p}_i$ by $k$. This allows for materials with varying elasticity, from perfectly rigid ($k=1$) to completely unstiff ($k=0$).

### Implementation: Distance Constraint Solver

```python
@ti.kernel
def solve_stretching_constraints(compliance: ti.f64, dt: ti.f64):
    """Solve stretching constraints using PBD projection - implements Equation {{eqref:eq:cloth:position_correction}}"""
    alpha = compliance / (dt * dt)
    for i in range(num_stretching_constraints):
        id0, id1 = stretching_ids[i, 0], stretching_ids[i, 1]
        w0, w1 = inv_mass[id0], inv_mass[id1]
        w_sum = w0 + w1
        if w_sum == 0.0: continue
        
        p0, p1 = pos[id0], pos[id1]
        delta = p0 - p1
        dist = delta.norm()
        if dist == 0.0: continue
        
        grad = delta / dist
        C = dist - stretching_lengths[i]
        s = -C / (w_sum + alpha)
        
        pos[id0] += s * w0 * grad
        pos[id1] -= s * w1 * grad
```

The kernel implements Equation {{eqref:eq:cloth:position_correction}} directly. The constraint violation `C` is the difference between current distance and rest length. The Lagrange multiplier `s` follows Equation {{eqref:eq:cloth:lambda_stretch}} with compliance softening. Position corrections are applied with opposite signs to maintain momentum conservation.

We need rest lengths to solve the constraints, so we compute all rest edge lengths once before the simulation:

```python
@ti.kernel
def init_physics():
    """Initialize physics state including rest lengths for constraints"""
    for i in range(num_stretching_constraints):
        id0, id1 = stretching_ids[i, 0], stretching_ids[i, 1]
        stretching_lengths[i] = (pos[id0] - pos[id1]).norm()
```

To identify which edges become constraints, we extract all unique edges from the triangle mesh topology:

```python
def find_constraint_indices(tri_ids_np):
    """Extract edge constraints from triangle mesh topology"""
    edge_to_tri_map = {}
    for i, tri in enumerate(tri_ids_np):
        for j in range(3):
            v0_idx, v1_idx = tri[j], tri[(j + 1) % 3]
            edge = tuple(sorted((v0_idx, v1_idx)))
            if edge not in edge_to_tri_map:
                edge_to_tri_map[edge] = []
            edge_to_tri_map[edge].append(i)
    
    stretching_ids = list(edge_to_tri_map.keys())
    return np.array(stretching_ids, dtype=np.int32)
```

This function processes the triangle mesh to identify all unique edges, which become stretching constraints. The `sorted` tuple ensures that each edge is represented consistently regardless of triangle orientation.
