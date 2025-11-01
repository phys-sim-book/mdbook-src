## Edge Constraints for Stretching Resistance

While volume constraints ensure incompressibility, edge constraints provide stretching resistance, preventing the mesh from deforming excessively under external forces. These constraints maintain the structural integrity of the volumetric mesh by constraining edge lengths to their rest values.

### Theory: Distance Constraint Formulation

Edge constraints in volumetric meshes are identical to the distance constraints used in cloth simulation. Each edge in the tetrahedral mesh is treated as a distance constraint between its two vertices.

> **{{def}}{def:mesh:edge_constraint}[Edge Distance Constraint]**
 Given two vertices with positions $\bm{p}_1$ and $\bm{p}_2$ connected by an edge with rest length $d$, the edge constraint function $C$ is defined as:
 $$
 {{numeq}}{eq:mesh:edge_constraint}
 C(\bm{p}_1, \bm{p}_2) = \|\bm{p}_1 - \bm{p}_2\| - d
 $$
 The constraint is satisfied when $C=0$.

The gradients of this function with respect to the vertex positions are:
$$
\nabla_{\bm{p}_1} C = \frac{\bm{p}_1 - \bm{p}_2}{\|\bm{p}_1 - \bm{p}_2\|} = \bm{n} \quad \text{and} \quad \nabla_{\bm{p}_2} C = -\frac{\bm{p}_1 - \bm{p}_2}{\|\bm{p}_1 - \bm{p}_2\|} = -\bm{n}
$$
where $\bm{n}$ is the unit vector along the edge.

Following the standard PBD projection formula, the scalar Lagrange multiplier $\lambda$ is computed as:
$$
{{numeq}}{eq:mesh:edge_lambda}
\lambda = \frac{C(\bm{p}_1, \bm{p}_2)}{w_1\|\nabla_{\bm{p}_1} C\|^2 + w_2\|\nabla_{\bm{p}_2} C\|^2} = \frac{\|\bm{p}_1 - \bm{p}_2\| - d}{w_1 + w_2}
$$
where $w_i = 1/m_i$ is the inverse mass of vertex $i$.

The position corrections are then:
$$
{{numeq}}{eq:mesh:edge_correction}
\Delta\bm{p}_1 = - \frac{w_1}{w_1+w_2}(\|\bm{p}_1 - \bm{p}_2\| - d)\bm{n} \\
\Delta\bm{p}_2 = + \frac{w_2}{w_1+w_2}(\|\bm{p}_1 - \bm{p}_2\| - d)\bm{n}
$$

These corrections distribute the constraint violation between the two vertices based on their inverse masses, ensuring momentum conservation.

### Implementation: Edge Constraint Solver

```python
@ti.kernel
def solve_edges(compliance: ti.f64, dt: ti.f64):
    """Solve edge constraints using PBD projection - implements Equation {{eqref:eq:mesh:edge_correction}}"""
    alpha = compliance / (dt * dt)
    for i in range(num_edges):
        id0, id1 = edge_ids[i, 0], edge_ids[i, 1]
        w0, w1 = inv_mass[id0], inv_mass[id1]
        w_sum = w0 + w1
        if w_sum == 0.0: continue
        
        delta = pos[id0] - pos[id1]
        dist = delta.norm()
        if dist == 0.0: continue
        
        grad = delta / dist
        C = dist - edge_lengths[i]
        s = -C / (w_sum + alpha)
        
        pos[id0] += s * w0 * grad
        pos[id1] -= s * w1 * grad
```

The kernel implements Equation {{eqref:eq:mesh:edge_correction}} directly. The constraint violation `C` is the difference between current distance and rest length. The Lagrange multiplier `s` follows Equation {{eqref:eq:mesh:edge_lambda}} with compliance softening. Position corrections are applied with opposite signs to maintain momentum conservation.

We need rest lengths to solve the constraints, so we compute all rest edge lengths once before the simulation:

```python
@ti.kernel
def init_physics():
    """Initialize physics state including rest lengths for edge constraints"""
    for i in range(num_edges):
        id0, id1 = edge_ids[i, 0], edge_ids[i, 1]
        edge_lengths[i] = (pos[id0] - pos[id1]).norm()
```

```python
def substep():
    pre_solve(sdt, 1)
    for _ in range(solver_iterations):
        solve_edges(edge_compliance, sdt)
        solve_volumes(vol_compliance, sdt)
    post_solve(sdt)
```

Edge constraints are solved before volume constraints in each iteration. This ordering allows volume constraints to work with a more stable mesh structure after edge constraints have provided local structural integrity.

```python
edge_compliance = 0.0  # Very stiff stretching (nearly inextensible)
vol_compliance = 0.0   # Very stiff volume (nearly incompressible)
```

Lower compliance values create stiffer materials. The relationship between edge and volume compliance determines material behavior: high edge compliance with low volume compliance creates soft, compressible material, while low values for both create stiff, incompressible material.
