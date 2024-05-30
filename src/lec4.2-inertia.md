## Inertia Term

For the inertia term, with \\(\tilde{x}^n = x^n + h v^n\\), we have
\\[
    E_I(x) = \frac{1}{2}\\|x - \tilde{x}^n \\|_M^2, \quad \nabla E_I(x) = M(x - \tilde{x}^n), \quad \text{and} \quad \nabla^2 E_I(x) = M,
\\]
which is straightforward to implement:

{{imp}}{imp:lec4:inertia_energy}[InertiaEnergy.py]
```python
{{#include solid-sim-tutorial/1_mass_spring/InertiaEnergy.py}}
```

The functions `val()`, `grad()`, and `hess()` are designed to compute different components of the inertia term. Specifically:

- `val()`: Computes the value of the inertia term.
- `grad()`: Calculates the gradient of the inertia term.
- `hess()`: Determines the Hessian of the inertia term.

Regarding the Hessian matrix, a memory-efficient approach is employed. Rather than allocating a large two-dimensional array to store all entries of the Hessian matrix, only the nonzero entries are kept. This is achieved using the `IJV` structure, which consists of three lists:

1. **Row Index**: Identifies the row position of each nonzero entry.
2. **Column Index**: Indicates the column position of each nonzero entry.
3. **Value**: The actual nonzero value at the specified row and column.

This method significantly reduces memory usage and computational costs associated with downstream processing.
