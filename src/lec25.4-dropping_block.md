## Case Study: Dropping Block*

Now, let’s implement the simple 2D case:

```python
{{#include solid-sim-tutorial/9_reduced_DOF/simulator.py}}
```

Here we use a small stiffness parameter making the body softer to amplify the deformation of the affine body to better demonstrate the difference between ABD and IPC. The blue is the collision mesh, and the red one is the embedding mesh. 

<figure>
    <center>
        <img src="img/lec25/abd_sim.gif">
        <figcaption><b>{{fig}}{fig:lec25:abd_sim}</b> An illustration of ABD simulation.</figcaption>
    </center>
</figure>

Now, let's examine the key modifications made to a standard IPC simulation to incorporate ABD.

Firstly, we introduce a function to compute the reduced basis. For our specific ABD case, we are modeling near-rigid affine transformations. Consequently, when calling this function, we specify `method=0` (polynomial basis) and `order=1` (linear basis) to obtain the linear basis:

```python
{{#include solid-sim-tutorial/9_reduced_DOF/utils.py:compute_reduced_basis}}
```

Next, the Hessian matrix and the gradient vector are projected onto the reduced degrees of freedom (DOFs). This projection is performed according to the chain rule, as described in Equation {{eqref: eq:lec25:newton_chain_rules}}. The relevant code snippet is shown here:
```python
{{#include solid-sim-tutorial/9_reduced_DOF/time_integrator.py:search_dir}}
```

These modifications allow us to derive and simulate the ABD dropping block example demonstrated earlier in the chapter.