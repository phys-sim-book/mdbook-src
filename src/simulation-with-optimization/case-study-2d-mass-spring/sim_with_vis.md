## Simulator with Visualization

Having gathered all necessary elements for our 2D mass-spring simulator, the next step is to implement the simulator. This implementation will operate in a step-by-step manner and include visualization capabilities to enhance understanding and engagement.

{{imp}}{imp:lec4:simulator}[simulator.py]
```python
{{#include solid-sim-tutorial/1_mass_spring/simulator.py}}
```

For 2D visualization in our simulator, we utilize the [Pygame library](https://www.pygame.org/). The simulation is initiated with a scene featuring a single square, which is initially elongated horizontally. During the simulation, the square begins to revert to its original horizontal dimensions. Subsequently, due to inertia, it will start to stretch vertically, oscillating back and forth until it eventually stabilizes at its rest shape, as illustrated in ({{ref: fig:lec4:stretched_square_sim}}).

<figure>
    <center>
    <img src="img/lec4/stretched_square.jpg">
    </center>
    <figcaption><b>{{fig}}{fig:lec4:stretched_square_sim}</b> From left to right: initial, intermediate, and final static frame of the initially stretched square simulation. </figcaption>
</figure>

In addition to storing node positions `x` and edges `e`, our simulation also requires allocating memory for several other key variables:

- **Node Velocities (`v`)**: To track the movement of each node over time.
- **Masses (`m`)**: Node masses are calculated by uniformly distributing the total mass of the square across each node. This is a preliminary approach; more detailed methods for calculating nodal mass in Finite Element Method (FEM) or Material Point Method (MPM) will be explored in future chapters.
- **Squared Rest Length of Edges (`l2`)**: Important for calculating the potential energy in the mass-spring system.
- **Spring Stiffnesses (`k`)**: A crucial parameter influencing the dynamics of the springs.

For visualization purposes beyond our simulator, we enable the export of the mesh data into `.obj` files. This is achieved by calling the `write_to_file()` function at the start and at each frame of the simulation. This feature facilitates the use of alternative visualization software to analyze and present the simulation results.

{{imp}}{imp:lec4:square_mesh_output}[Output Square Mesh, square_mesh.py]
```python
{{#include solid-sim-tutorial/1_mass_spring/square_mesh.py:write_to_file}}
```

With all components properly set up, the next phase involves initiating the simulation loop. This loop advances the time integration and visualizes the results at each time step. To execute the simulation program, the following command is used in the terminal:
```python
python3 simulator.py
```

> **{{rem}}{rem:lec4:sim_remarks}[Practical Considerations]**
> With our simulator implementation in place, it provides us with the flexibility to experiment with various configurations of the optimization time integration scheme. Such testing is invaluable for gaining deeper insights into the roles and impacts of each essential component.
>
> Consider an example: if we opt not to project the mass-spring Hessian to a Symmetric Positive Definite (SPD) form, peculiar behaviors may emerge under certain conditions. For instance, running the simulation with a frame-rate time step size of `h=0.02` and an `initial_stretch` of `0.5` could lead to line search failures. This, in turn, results in very small step sizes, hampering the optimization process and preventing significant progress.
>
>While line search might seem superfluous in this simplistic 2D example, its necessity becomes apparent in more complex 3D elastodynamics simulations, especially those involving large deformations. Here, line search is crucial to ensure the convergence of the simulation.
>
>Another point of interest is the stopping criteria applied in traditional solids simulators. Many such simulators forego a dynamic stopping criterion and instead terminate the optimization process after a predetermined number of iterations. This approach, while straightforward, can lead to numerical instabilities or 'explosions' in more challenging scenarios. This underscores the importance of carefully considering the integration scheme and its parameters to ensure stable and accurate simulations.
