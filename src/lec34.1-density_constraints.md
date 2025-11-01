# Density Constraints in Position-Based Fluids

## The Per-Particle Density Constraint

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

## Implementation: Kernel Functions

The PBF implementation requires two different kernel functions, each serving a specific purpose:

**Poly6 Kernel for Density Estimation**

The Poly6 kernel is used to estimate particle density by smoothly weighting neighboring particles:

```python
@ti.func
def poly6_value(s, h_):
    """Poly6 kernel for density estimation - implements Equation {{eqref:eq:pbf:sph_density}}"""
    result = 0.0
    if 0 < s and s < h_:
        x = (h_ * h_ - s * s) / (h_ * h_ * h_)
        result = poly6_factor * x * x * x
    return result
```

This kernel provides smooth density estimation with a compact support radius `h`. The cubic falloff ensures that distant particles have minimal influence on density calculations, creating realistic fluid behavior.

**Spiky Kernel for Position Corrections**

The Spiky kernel is used for computing position corrections because it has a non-vanishing gradient near the origin, preventing particle clustering:

```python
@ti.func  
def spiky_gradient(r, h_):
    """Spiky kernel gradient for position corrections - used in Equation {{eqref:eq:pbf:position_correction}}"""
    result = ti.Vector([0.0, 0.0, 0.0])
    r_len = r.norm()
    if 0 < r_len and r_len < h_:
        x = (h_ - r_len) / (h_ * h_ * h_)
        g_factor = spiky_grad_factor * x * x
        result = r * g_factor / r_len
    return result
```

The Spiky kernel's linear falloff provides strong repulsive forces when particles are close together, which is essential for maintaining proper particle spacing and preventing clustering artifacts.

## Implementation: Density Constraint Solver

The density constraint solver implements the three-phase PBD algorithm described in the theory section:

**Phase 1: Compute Lagrange Multipliers**

```python
@ti.kernel
def solve_density_constraints():
    # Phase 1: Compute Lagrange multipliers λ_i for each particle
    for p_i in positions:
        pos_i = positions[p_i]
        grad_i, sum_gradient_sqr, density_constraint = ti.Vector([0.0, 0.0, 0.0]), 0.0, 0.0

        for j in range(particle_num_neighbors[p_i]):
            p_j = particle_neighbors[p_i, j]
            pos_ji = pos_i - positions[p_j]
            grad_j = spiky_gradient(pos_ji, h)
            grad_i += grad_j
            sum_gradient_sqr += grad_j.dot(grad_j)
            density_constraint += poly6_value(pos_ji.norm(), h)

        # Implement Equation {{eqref:eq:pbf:constraint_func}}: C_i = ρ_i/ρ₀ - 1
        density_constraint = (mass * density_constraint / rho0) - 1.0
        sum_gradient_sqr += grad_i.dot(grad_i)
        
        # Implement Equation {{eqref:eq:pbf:lambda_cfm}}: CFM regularization
        denominator = (1.0 / mass) * sum_gradient_sqr + lambda_epsilon
        lambdas[p_i] = -density_constraint / denominator if denominator > epsilon else 0.0
```

This phase computes the constraint violation for each particle and calculates the corresponding Lagrange multiplier. The CFM regularization prevents numerical instability when particles have few neighbors.

**Phase 2: Compute Position Corrections**

```python
    # Phase 2: Compute position corrections using Equation {{eqref:eq:pbf:position_correction}}
    for p_i in positions:
        pos_i = positions[p_i]
        lambda_i = lambdas[p_i]
        pos_delta_i = ti.Vector([0.0, 0.0, 0.0])

        for j in range(particle_num_neighbors[p_i]):
            p_j = particle_neighbors[p_i, j]
            lambda_j = lambdas[p_j]
            pos_ji = pos_i - positions[p_j]
            pos_delta_i += (lambda_i + lambda_j) * spiky_gradient(pos_ji, h)
        
        position_deltas[p_i] = pos_delta_i / rho0
```

This phase computes the position correction for each particle using the symmetric constraint formula. The correction depends on both the particle's own constraint violation and those of its neighbors.

**Phase 3: Apply Position Corrections**

```python
    # Phase 3: Apply position corrections
    for i in positions:
        positions[i] += position_deltas[i]
```

The final phase applies the computed corrections to update particle positions, satisfying the density constraints.
