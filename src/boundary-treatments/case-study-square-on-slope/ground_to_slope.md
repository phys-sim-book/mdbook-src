## From Ground to Slope

The implementation in the [Square Drop](lec8.3-square_drop.md) case study for horizontal grounds results in a simplified distance and distance gradient (Equation {{eqref: eq:lec8:dist_ground}}) compared to that of a general half-space (Equation {{eqref: eq:lec7:half_space_dist}}):
$$
    d(\mathbf{x}) = \mathbf{n}^T (\mathbf{x} - \bm{o}), \quad
    \nabla d(\mathbf{x}) = \mathbf{n}, \quad \text{and} \quad 
    \nabla^2 d(\mathbf{x}) = \bm{0}.
    {{numeq}}{eq:lec10:half_space_dist}
$$
This is all we need for implementing the slope. Defining a normal direction and a point lying on the slope

{{imp}}{imp:lec10:slope}[Slope setup, simulator.py]
```python
{{#include solid-sim-tutorial/4_friction/simulator.py:slope_setup}}
```
and passing them to the time integrator and barrier energy, we can modify the barrier energy value, gradient, and Hessian computation for the slope as

{{imp}}{imp:lec10:slope_barrier}[Slope contact barrier, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/4_friction/BarrierEnergy.py:slope_barrier}}
```

Then for the continuous collision detection, we similarly modify the implementation to compute the large feasible initial step size for line search using $\mathbf{n}$ and $\bm{o}$:

{{imp}}{imp:lec10:slope_ccd}[Slope CCD, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/4_friction/BarrierEnergy.py:init_step_size}}
```
Here the search direction of each node is projected onto the normal direction $\mathbf{n}$ to divide the current distance when computing the smallest step size that first brings the distance to $0$.

Finally, drawing the slope as a line from $\bm{o} - 3\hat{n}$ to $\bm{o} + 3\hat{n}$ where $\hat{n} = [\mathbf{n}_y, -\mathbf{n}_x]$ pointing to the inclined direction,

{{imp}}{imp:lec10:slope_vis}[Slope visualization, simulator.py]
```python
{{#include solid-sim-tutorial/4_friction/simulator.py:slope_vis}}
```
we can now simulate an elastic square dropped on a slope without friction ({{ref: fig:lec10:slope_mu0}}).
<figure>
    <center>
    <img src="img/lec10/slope_mu0.jpg">
    </center>
    <figcaption><b>{{fig}}{fig:lec10:slope_mu0}</b> An elastic square dropped onto a frictionless slope, bouncing as it slides down. </figcaption>
</figure>
