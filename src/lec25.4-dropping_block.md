## Case Study: ABD Square Drop*

Let’s now implement Affine Body Dynamics (ABD) in 2D for a neo-Hookean version of the [square drop](./lec8.3-square_drop.md) simulation. This requires only minor modifications to the standard IPC simulation code. The full implementation is available in the `9_reduced_DOF` folder of our [solid simulation tutorial](https://github.com/phys-sim-book/solid-sim-tutorial).

We begin by introducing a function to compute the reduced basis. In ABD, we consider only affine deformations. Therefore, we use `method=1` (polynomial basis) and `order=1` (linear basis) to extract the linear basis:

{{imp}}{imp:lec25:compute_reduced_basis}[Compute reduced basis, utils.py]
```python
{{#include solid-sim-tutorial/9_reduced_DOF/utils.py:compute_reduced_basis}}
```

Here, `method=0` refers to full-space simulation and returns immediately without computing a basis.  
`method=1` computes polynomial bases, including linear, quadratic, and cubic functions. For each basis vector, the displacement components at each node are expressed as polynomial functions of the node’s material-space coordinates. 
`method=2` computes bases via linear modal analysis, which solves the eigensystem of the elasticity Hessian and extracts displacement fields that correspond to the deformation modes that increases the least amount of energy. This will be discussed in more detail in [the next lecture](./lec25.6-modal_reductions.md).

After computing the basis, we restrict the simulation to the corresponding subspace by projecting the Hessian matrix and the gradient vector. This projection follows the chain rule, as described in Equation {{eqref: eq:lec25:newton_chain_rules}}. The relevant implementation is:

{{imp}}{imp:lec25:search_dir}[Compute reduced search direction, time_integrator.py]
```python
{{#include solid-sim-tutorial/9_reduced_DOF/time_integrator.py:search_dir}}
```

These changes enable us to run the ABD version of the square-drop simulation:

<figure>
    <center>
        <img src="img/lec25/abd_sim.gif">
        <figcaption><b>{{fig}}{fig:lec25:abd_sim}</b> ABD simulation of a square dropped onto the ground.</figcaption>
    </center>
</figure>

In this example, we reduce the stiffness parameter to make the body softer, emphasizing the difference between ABD and standard IPC. The blue mesh is the original mesh (also used for collision), while the red triangle visualizes the reduced degrees of freedom.
