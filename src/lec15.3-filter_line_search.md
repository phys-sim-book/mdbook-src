## Filter Line Search for Non-Inversion

To guarantee non-inversion just like non-interpenetration (see [Filter Line Search](lec8-filter_line_search.md)) during the simulation, we can similarly filter the line search initial step size with a critical step size $\alpha^I$ that first brings the volume of any triangles to $0$. This can be obtained by solving a 1D equation per triangle:
$$
V(\mathbf{x}_i + \alpha^I \mathbf{p}_i) = 0,
{{numeq}}{eq:lec15:V_eq_0}
$$
and taking the minimum of the solved step sizes. Here $\mathbf{p}_i$ is the search direction of node $i$, and in 2D, Equation {{eqref:eq:lec15:V_eq_0}} is equivalent to:
$$
\det([\mathbf{x}^\alpha_{21}, \mathbf{x}^\alpha_{31}]) \equiv \mathbf{x}^\alpha_{21,1} \mathbf{x}^\alpha_{31,2} - \mathbf{x}^\alpha_{21,2} \mathbf{x}^\alpha_{31,1} = 0
{{numeq}}{eq:lec15:det_tri_equals_0}
$$
with $\mathbf{x}^\alpha_{ij} = \mathbf{x}_{ij} + \alpha^I \mathbf{p}_{ij}$ and $\mathbf{x}_{ij} = \mathbf{x}_i - \mathbf{x}_j$, $\mathbf{p}_{ij} = \mathbf{p}_i - \mathbf{p}_j$. Expanding Equation {{eqref:eq:lec15:det_tri_equals_0}} we obtain:
$$
(\mathbf{x}_{21,1} + \alpha^I \mathbf{p}_{21,1}) (\mathbf{x}_{31,2} + \alpha^I \mathbf{p}_{31,2}) - (\mathbf{x}_{21,2} + \alpha^I \mathbf{p}_{21,2}) (\mathbf{x}_{31,1} + \alpha^I \mathbf{p}_{31,1}) = 0,
$$
which can be reorganized as a quadratic equation of $\alpha^I$:
$$
\det([\mathbf{p}_{21}, \mathbf{p}_{31}]) (\alpha^I)^2 +
(\det([\mathbf{x}_{21}, \mathbf{p}_{31}]) + \det([\mathbf{p}_{21}, \mathbf{x}_{31}])) \alpha^I +
\det([\mathbf{x}_{21}, \mathbf{x}_{31}]) = 0.
$$
Here, note that $\det([\mathbf{p}_{21}, \mathbf{p}_{31}])$ can be very tiny when the nodes do not move much or when their movement barely changes to triangle area in the current timestep, thus the equation can be degenerated into a linear one. To robustly detect this degenerate case, we cannot directly check whether $\det([\mathbf{p}_{21}, \mathbf{p}_{31}])$ is $0$ due to numerical errors. In fact, checking whether $\det([\mathbf{p}_{21}, \mathbf{p}_{31}])$ is below an epsilon is still tricky, because the scale of $\det([\mathbf{p}_{21}, \mathbf{p}_{31}])$ heavily depends on the scene dimension and nodal velocity during the simulation. Therefore, we use $\det([\mathbf{x}_{21}, \mathbf{x}_{31}]) $ as a scaling and obtain a scaled but equivalent equation:
$$
\frac{\det([\mathbf{p}_{21}, \mathbf{p}_{31}])}{\det([\mathbf{x}_{21}, \mathbf{x}_{31}])} (\alpha^I)^2 +
\frac{\det([\mathbf{x}_{21}, \mathbf{p}_{31}]) + \det([\mathbf{p}_{21}, \mathbf{x}_{31}])}{\det([\mathbf{x}_{21}, \mathbf{x}_{31}]) } \alpha^I + 1 = 0,
{{numeq}}{eq:lec15:scaled_quad_eq_of_alphaI}
$$
where magnitude checks can be safely performed on any coefficients with unitless thresholds.

In practice, we also need to allow some slackness so that the step size to be taken will not lead to an exactly $0$ volume. Thus, we solve $\alpha^I$ such that it first decreases the volume of any triangles by $90\%$, which can be realized by modifying the constant term coefficient in Equation {{eqref: eq:lec15:scaled_quad_eq_of_alphaI}} from $1$ to $0.9$:



{{imp}}{imp:lec15:filter_line_search}[Filter line search, NeoHookeanEnergy.py]
```python
{{#include solid-sim-tutorial/6_inv_free/NeoHookeanEnergy.py:filter_line_search}}
```
Here, if the equation does not have a positive real root, that means for this specific triangle, the step size can be taken arbitrarily large and it will not trigger inversion.

The quadratic equation can be solved as

{{imp}}{imp:lec15:find_positive_real_root}[Solve quadratic equation, utils.py]
```python
{{#include solid-sim-tutorial/6_inv_free/utils.py:find_positive_real_root}}
```
With scaled coefficients, we simply use a unitless threshold, e.g. \code{1e-6}, to check for degeneracies. If no positive real roots are found, the function simply returns $-1$.

Now as we filter the initial step size in addition to non-interpenetration:

{{imp}}{imp:lec15:apply_filter}[Apply filter, time_integrator.py]
```python
{{#include solid-sim-tutorial/6_inv_free/time_integrator.py:apply_filter}}
```
and make sure all added data structures and modified functions are reflected in the time integrator, we can finally simulate the compressing square example from [Moving Boundary Condition](lec11-mov_DBC.md) with guaranteed non-inversion (see {{ref: fig:lec15:compress_square_inv_free}}).

<figure>
    <center>
    <img src="img/lec15/inv_free_compress_square.jpg">
    </center>
    <figcaption><b>{{fig}}{fig:lec15:compress_square_inv_free}</b> A square is dropped onto the ground and compressed severely by a ceiling while maintaining inversion-free throughout the simulation. The ground has friction coefficient $0.11$ so that the bottom of the square slides less than the top, where the ceiling has no friction. </figcaption>
</figure>