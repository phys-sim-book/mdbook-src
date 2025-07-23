## Case Study: Dropping Block*

Now, let’s implement ABD in 2D for the [square on slope](./lec10-square_on_slope.md) simulation.
This just need the modification of a few lines of the standard IPC simulation code.

First, we introduce a function to compute the reduced basis. For our specific ABD case, we are modeling near-rigid affine transformations. Consequently, when calling this function, we specify `method=0` (polynomial basis) and `order=1` (linear basis) to obtain the linear basis:

```python
{{#include solid-sim-tutorial/9_reduced_DOF/utils.py:compute_reduced_basis}}
```

<!-- basis calculation, file description -->

Next, the Hessian matrix and the gradient vector are projected onto the reduced degrees of freedom (DOFs). This projection is performed according to the chain rule, as described in Equation {{eqref: eq:lec25:newton_chain_rules}}. The relevant code snippet is shown here:
```python
{{#include solid-sim-tutorial/9_reduced_DOF/time_integrator.py:search_dir}}
```

These modifications allow us to simulate the ABD version of square on slope example:

<figure>
    <center>
        <img src="img/lec25/abd_sim.gif">
        <figcaption><b>{{fig}}{fig:lec25:abd_sim}</b> An illustration of ABD simulation.</figcaption>
    </center>
</figure>

Here we use a small stiffness parameter making the body softer to amplify the deformation of the affine body to better demonstrate the difference between ABD and IPC. The blue is the collision mesh, and the red one is the embedding mesh. 