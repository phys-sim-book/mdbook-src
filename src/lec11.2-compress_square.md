## Case Study: Compressing Square

We simulate compressing an elastic square using a ceiling. 
The excutable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `5_mov_dirichlet` folder.

The ceiling in our simulation is modeled as a half-space with a downward normal vector $\mathbf{n}=(0,-1)$. The distance from the ceiling to other simulated Degrees of Freedom (DOFs) can be calculated using Equation {{eqref: eq:lec7:half_space_dist}}. To effectively apply the penalty method, it's necessary that the ceiling's height also serves as a DOF.

Following the approach used in the [Square on Slope](lec10-square_on_slope.md) project, we choose the origin $\bm{o}$ on the ceiling as the DOF and incorporate it into the variable $x$:

{{imp}}{imp:lec11:ceiling_dof}[Ceiling DOF setup, simulator.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/simulator.py:ceiling_dof}}
```

The ceiling is initially positioned directly above the elastic square, as shown in the left image of {{ref: fig:lec11:compress_square}}. By doing so, we ensure that the nodal mass of this newly added DOF is consistent with the other simulated nodes on the square, as per our implementation.

With this additional DOF, we can straightforwardly model the contact between the ceiling and the square. This is done by enhancing the existing functions that compute the barrier energy value, gradient, Hessian, and the initial step size:

{{imp}}{imp:lec11:barrier_val}[Barrier energy value, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/BarrierEnergy.py:ceiling_val}}
```
{{imp}}{imp:lec11:barrier_grad}[Barrier energy gradient, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/BarrierEnergy.py:ceiling_grad}}
```
{{imp}}{imp:lec11:barrier_hess}[Barrier energy Hessian, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/BarrierEnergy.py:ceiling_hess}}
```
{{imp}}{imp:lec11:barrier_ccd}[Initial step size calculation, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/BarrierEnergy.py:ceiling_ccd}}
```
Here for the distance between the ceiling $\bm{o}$ and a node $\mathbf{x}$, we have the stacked quantities locally:
$$
    d(\mathbf{x}, \bm{o}) = \mathbf{n}^T (\mathbf{x} - \bm{o}), \quad 
    \nabla d(\mathbf{x}, \bm{o}) = 
    \begin{bmatrix}
        \mathbf{n} \\ 
        -\mathbf{n}
    \end{bmatrix}, \quad
    \nabla^2 d(\mathbf{x}, \bm{o}) = \bm{0}.
$$

Now we apply the moving BC on the ceiling to compress the elastic square. We set the ceiling's DOF, identified by the node index `(n_seg+1)*(n_seg+1)`, as the sole Dirichlet Boundary Condition (DBC) in this scene. We assign it a downward velocity of $(0, -0.5)$. The movement is stopped when the ceiling reaches a height of $-0.6$:

{{imp}}{imp:lec11:dbc_setup}[DBC setup, simulator.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/simulator.py:ceiling_dbc_setup}}
```

Then we implement the penalty term according to Equation {{eqref: eq:lec11:DBC_penalty}}, which is essentially a quadratic spring energy for controlling the motion of the ceiling:

{{imp}}{imp:lec11:spring_energy}[Spring energy computation, SpringEnergy.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/SpringEnergy.py}}
```

Next, we focus on optimizing with the spring energies while properly handling the convergence check and penalty stiffness adjustments. At the start of each time step, the target position for each DBC node is computed, and the penalty stiffness, $k_\text{M}$, is initialized to $10$. If certain nodes reach their preset limit, we then set the target as their current position:

{{imp}}{imp:lec11:dbc_initialization}[DBC initialization, time_integrator.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/time_integrator.py:dbc_initialization}}
```

Entering the Newton loop, in each iteration, just before computing the search direction, we assess how many DBC nodes are close enough to their target positions. We store these results in the variable `DBC_satisfied`:

{{imp}}{imp:lec11:dbc_check}[DBC satisfaction check, time_integrator.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/time_integrator.py:dbc_check}}
```
Then we only eliminate the DOFs of those DBC nodes that already satisfy the boundary condition:

{{imp}}{imp:lec11:dof_elimination}[DOF elimination, time_integrator.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/time_integrator.py:dof_elimination}}
```
The BC satisfaction information stored in `DBC_satisfied` is also used to check convergence and update $k_\text{M}$ when needed:

{{imp}}{imp:lec11:convergence_criteria}[Convergence criteria, time_integrator.py]
```python
{{#include solid-sim-tutorial/5_mov_dirichlet/time_integrator.py:convergence_criteria}}
```

Now, we proceed to run the simulation, which involves severely compressing the dropped elastic square as depicted in ({{ref: fig:lec11:compress_square}}). From the final static frame, we observe that the elastic springs on the edges are inverted due to extreme compression. This artifact is typical in mass-spring models of elasticity. In future chapters, we will explore how applying finite-element discretization to barrier-type elasticity models, such as the Neo-Hookean model, can prevent such issues. That approach is akin to the enforcement of non-interpenetrations in our current simulations.

<figure>
    <center>
    <img src="img/lec11/compress_square.jpg">
    </center>
    <figcaption><b>{{fig}}{fig:lec11:compress_square}</b> A square is dropped onto the ground and compressed by a ceiling until inverted. </figcaption>
</figure>