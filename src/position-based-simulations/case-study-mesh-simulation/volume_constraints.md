## Volume Conservation Constraints

The simulation of incompressible or nearly incompressible materials is critical for many applications in computer graphics. In a force-based framework, incompressibility typically requires solving a complex Poisson equation for pressure. PBD offers a more direct and often simpler approach by enforcing volume conservation through geometric constraints.

### Theory: Tetrahedral Volume Constraint Formulation

For volumetric objects discretized into a tetrahedral mesh, incompressibility can be approximated by constraining the volume of each individual tetrahedron to remain at its rest volume.

> **{{def}}{def:volume:tetrahedral}[Tetrahedral Volume Constraint]**
 Given a tetrahedron defined by four vertices with positions $\bm{p}_1, \bm{p}_2, \bm{p}_3, \bm{p}_4$ and a rest volume $V_0$, the volume constraint function $C$ is defined as:
 $$
 {{numeq}}{eq:volume:tetra_constraint}
 C(\bm{p}_1, \bm{p}_2, \bm{p}_3, \bm{p}_4) = \frac{1}{6} \left( (\bm{p}_2 - \bm{p}_1) \times (\bm{p}_3 - \bm{p}_1) \right) \cdot (\bm{p}_4 - \bm{p}_1) - V_0
 $$
 The constraint is satisfied when $C=0$.

The gradients of this function with respect to the four vertex positions are:
$$
\begin{aligned}
\nabla_{\bm{p}_1} C &= \frac{1}{6} \left( (\bm{p}_3 - \bm{p}_2) \times (\bm{p}_4 - \bm{p}_2) \right) \\
\nabla_{\bm{p}_2} C &= \frac{1}{6} \left( (\bm{p}_4 - \bm{p}_3) \times (\bm{p}_1 - \bm{p}_3) \right) \\
\nabla_{\bm{p}_3} C &= \frac{1}{6} \left( (\bm{p}_1 - \bm{p}_4) \times (\bm{p}_2 - \bm{p}_4) \right) \\
\nabla_{\bm{p}_4} C &= \frac{1}{6} \left( (\bm{p}_2 - \bm{p}_1) \times (\bm{p}_3 - \bm{p}_1) \right)
\end{aligned}
$$

These gradients—vectors proportional to the areas of the opposing faces—are used to calculate position corrections for the four vertices. Following the standard PBD projection mechanism, the Lagrange multiplier $\lambda$ is computed as:

$$
{{numeq}}{eq:volume:lambda}
\lambda = \frac{C(\bm{p}_1, \bm{p}_2, \bm{p}_3, \bm{p}_4)}{\sum_{i=1}^{4} w_i \|\nabla_{\bm{p}_i} C\|^2}
$$

where $w_i = 1/m_i$ is the inverse mass of vertex $i$. The position corrections are then:

$$
{{numeq}}{eq:volume:position_correction}
\Delta\bm{p}_i = -\lambda w_i \nabla_{\bm{p}_i} C
$$

### Implementation: Volume Constraint Solver

```python
@ti.kernel
def solve_volumes(
    compliance: ti.f64, dt: ti.f64, num_tets: ti.i32,
    pos: ti.template(), tet_ids: ti.template(), rest_vol: ti.template(), inv_mass: ti.template(),
    vol_id_order: ti.template(), lambdas: ti.template()):
    """Solve volume constraints using PBD projection - implements Equation {{eqref:eq:volume:position_correction}}"""
    alpha = compliance / (dt * dt)
    for i in range(num_tets):
        p_indices = ti.Vector([tet_ids[i, 0], tet_ids[i, 1], tet_ids[i, 2], tet_ids[i, 3]])
        w_sum = 0.0
        grads = ti.Matrix.zero(ti.f64, 4, 3)
        for j in ti.static(range(4)):
            ids = ti.Vector([p_indices[vol_id_order[j, c]] for c in range(3)])
            p0, p1, p2 = pos[ids[0]], pos[ids[1]], pos[ids[2]]
            grad = (p1 - p0).cross(p2 - p0) / 6.0
            grads[j, :] = grad
            w_sum += inv_mass[p_indices[j]] * grad.norm_sqr()
        if w_sum == 0.0: continue
        C = get_tet_volume(p_indices, pos) - rest_vol[i]
        dlambda = -(C + alpha * lambdas[i]) / (w_sum + alpha)
        lambdas[i] += dlambda
        for j in ti.static(range(4)):
            pos[p_indices[j]] += dlambda * inv_mass[p_indices[j]] * grads[j, :]
```

The kernel computes gradients using the cross product formula from Equation {{eqref:eq:volume:tetra_constraint}}. The `vol_id_order` array ensures proper vertex ordering for the cross products. The constraint violation `C` is computed as the difference between current and rest volume, and the Lagrange multiplier `s` follows Equation {{eqref:eq:volume:lambda}} with compliance softening.

```python
@ti.func
def get_tet_volume(p_indices):
    """Calculate tetrahedral volume using scalar triple product - implements Equation {{eqref:eq:volume:tetra_constraint}}"""
    p0, p1, p2, p3 = pos[p_indices[0]], pos[p_indices[1]], pos[p_indices[2]], pos[p_indices[3]]
    return (p1 - p0).cross(p2 - p0).dot(p3 - p0) / 6.0
```

This function implements the volume calculation from Equation {{eqref:eq:volume:tetra_constraint}} using the scalar triple product formula.

```python
@ti.kernel
def init_physics():
    """Initialize physics state including rest volumes for constraints"""
    inv_mass.fill(0.0)
    for i in range(num_tets):
        p_indices = ti.Vector([tet_ids[i, 0], tet_ids[i, 1], tet_ids[i, 2], tet_ids[i, 3]])
        vol = get_tet_volume(p_indices)
        rest_vol[i] = vol
        if vol > 0.0:
            p_inv_mass = 1.0 / (vol / 4.0)
            for j in ti.static(range(4)):
                inv_mass[p_indices[j]] += p_inv_mass
```

The rest volumes are computed once during initialization and stored for constraint solving. Mass is distributed based on tetrahedral volumes, so vertices belonging to larger tetrahedra have more mass.
