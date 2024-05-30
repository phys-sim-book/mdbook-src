## Case Study: Hanging Square*
We use a simple case study to end this lecture. Based on the [mass-spring system](lec4-2d_mass_spring.md) developed in a previous section, we implement gravitational energy and sticky Dirichlet boundary conditions to simulate a hanging square.
The excutable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `2_dirichlet` folder.

Gravitational energy has
$$
    P(x) = -x^T Mg, \quad \nabla P(x) = -Mg, \quad \text{and} \quad \nabla^2 P(x) = 0,
$$
which can be trivially implemented:

{{imp}}{imp:lec5:GravityEnergy}[GravityEnergy.py]
```python
{{#include solid-sim-tutorial/2_dirichlet/GravityEnergy.py}}
```
Then we just need to make sure the gravitational energy is added into the Incremental Potential (IP):

{{imp}}{imp:lec5:time_integrator_1}[Adding gravity to IP, time_integrator.py]
```python
{{#include solid-sim-tutorial/2_dirichlet/time_integrator.py:ADDING_GRAVITY}}
```

For the sticky Dirichlet boundary condition, we modify the system accordingly when computing search direction:

{{imp}}{imp:lec5:time_integrator_2}[DOF elimination, time_integrator.py]
```python
{{#include solid-sim-tutorial/2_dirichlet/time_integrator.py:search_dir}}
```
Here `is_DBC` is an array marking whether a node is Dirichlet or not as we store the Dirichlet node indices in `DBC`:

{{imp}}{imp:lec5:time_simulator_1}[DBC definition, simulator.py]
```python
{{#include solid-sim-tutorial/2_dirichlet/simulator.py:DBC_def}}

# ...

{{#include solid-sim-tutorial/2_dirichlet/simulator.py:DBC_mask}}
```

Finally, after making sure `is_DBC` is passed to the time integrator, we can simulate an energetic hanging square (no initial stretching) with a smaller spring stiffness `k=1e3` at framerate time step size `h=0.02` ({{ref: fig:lec5:hanging_square_sim}}).

<figure>
    <center>
    <img src="img/lec5/hanging_square.jpg" width="550">
    </center>
    <figcaption><b>{{fig}}{fig:lec5:hanging_square_sim}</b> From left to right: initial, intermediate, and final static frame of the hanging square simulation. </figcaption>
</figure>