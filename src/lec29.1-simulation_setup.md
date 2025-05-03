## Simulation Setup

In this section, we define the physical and numerical setup required for implementing a minimal MPM simulation of **Two Colliding Elastic Blocks in 2D**. We walk through the definition of simulation properties, initialization of particle positions and velocities, and data structures used throughout the simulation.

### Physical and Numerical Properties

We begin by setting up the resolution and discretization of the simulation domain:

{{imp}}{imp:lec29:property_def}[Physical and Numerical Properties, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:property_def}}
```

These parameters define a uniform dense background grid, particle resolution, and time integration step size. The entire simulation domain spans from `[0, 0]` to `[1, 1]` meters, and we aim for around 8 particles per grid cell on average.

### Initial Particle Sampling and Scene Setup

We sample particles from two rectangular regions using **uniform grid sampling**. These two boxes are placed symmetrically on the left and right sides of the domain and are initialized with opposite velocities to simulate a head-on collision.

Compared to Poisson sampling, uniform sampling is easier to implement for analytic shapes, such as boxes and spheres, due to its structured nature and simple parametrization. However, this regularity can lead to **aliasing artifacts**, such as visible patterns or striping in the simulation, which may introduce unnatural structured noise into the result.

Here we adopt uniform sampling **for simplicity and clarity**, keeping the focus on the MPM pipeline itself.

{{imp}}{imp:lec29:setting}[Initial Particle Sampling and Scene Setup, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:setting}}
```

Each block consists of uniformly distributed material points representing a homogeneous elastic body. The left block is given an initial velocity of \([+10, 0]\) m/s, and the right block \([-10, 0]\) m/s, setting up a symmetric, head-on collision scenario with **zero net linear momentum**. This configuration mimics a controlled impact experiment.

### Material Particles and Grid Data

We define data fields to represent the state of each material point (particle) and background grid node. For particles, this includes position, velocity, volume, mass, and deformation gradient, following [Material Particles](./lec26.1-material_particles.md). For the grid, we define nodal mass and velocity fields using dense arrays, which are sufficient for small-scale simulations. These can be further optimized using sparse grid structures—a direction we leave as future work for interested readers.

{{imp}}{imp:lec29:data_def}[Material Particles and Grid Data, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:data_def}}
```