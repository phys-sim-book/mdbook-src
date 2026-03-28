## Tensile Instability Correction

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

### Implementation: Tensile Instability Correction

The tensile instability correction is implemented as an additional artificial pressure term that creates repulsive forces between nearby particles:

**Computing the Artificial Pressure Term**

```python
@ti.func
def compute_scorr(pos_ji):
    """Tensile instability correction - implements Equation {{eqref:eq:pbf:scorr}}"""
    x = poly6_value(pos_ji.norm(), h) / poly6_value(corr_deltaQ_coeff * h, h)
    x = x * x; x = x * x  # pow(x, 4)
    return -corrK * x
```

This function computes the artificial pressure term `s_corr` that scales with the kernel value ratio. The fourth power ensures that the correction is strongest when particles are very close together, creating effective repulsion.

**Integration into Position Corrections**

The artificial pressure term is integrated into the position correction calculation:

```python
# Integration into density constraint solver
for j in range(particle_num_neighbors[p_i]):
    p_j = particle_neighbors[p_i, j]
    lambda_j = lambdas[p_j]
    pos_ji = pos_i - positions[p_j]
    scorr_ij = compute_scorr(pos_ji)  # Tensile instability correction
    pos_delta_i += (lambda_i + lambda_j + scorr_ij) * spiky_gradient(pos_ji, h)
```

The artificial pressure term is added to the standard constraint correction, creating short-range repulsive forces that prevent particle clumping near free surfaces while maintaining overall density constraint satisfaction.
