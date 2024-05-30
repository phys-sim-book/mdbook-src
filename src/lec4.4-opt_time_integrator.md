## Optimization Time Integrator

Having established the capability to evaluate the Incremental Potential for arbitrary configurations, we now turn our attention to the implementation of the optimization time integrator. This integrator is crucial for minimizing the Incremental Potential, which in turn updates the nodal positions and velocities. This implementation follows the approach outlined in {{ref: alg:lec3:PN_method_IE}}:


{{imp}}{imp:lec4:time_integrator}[time_integrator.py]
```python
{{#include solid-sim-tutorial/1_mass_spring/time_integrator.py}}
```

Here `step_forward()` is essentially a direct translation of the projected Newton method with line search ({{ref: alg:lec3:PN_method_IE}}), and we implemented the Incremental Potential value (`IP_val()`), gradient (`IP_grad()`), and Hessian (`IP_hess()`) evaluations as separate functions for clarity. 

For the computation of search directions, we utilize the linear solver from the [Scipy library](https://scipy.org/), which is adept at handling sparse matrices. Notably, this solver accepts matrices in the Compressed Sparse Row (CSR) format. The choice of this format and solver is driven by their efficiency in processing and memory usage, which is particularly advantageous when dealing with large-scale problems with large sparse matricies often encountered in computational simulations.
