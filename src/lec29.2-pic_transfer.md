## Particle-In-Cell Transfer

At the beginning of each simulation step, the grid must be cleared before accumulating new particle-to-grid transfers.

{{imp}}{imp:lec29:reset_grid}[Reset Grid, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:reset_grid}}
```

We adopt the Saint Venant–Kirchhoff (StVK) constitutive model formulated in the logarithmic (Hencky) strain space using the SVD of the deformation gradient.

{{imp}}{imp:lec29:stvk}[Stvk Hencky Elasticity, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:stvk}}
```


During the particle-to-grid (P2G) transfer, we use quadratic B-spline interpolation to distribute each particle’s mass, momentum, and internal force to its neighboring grid nodes. This process follows the PIC (Particle-In-Cell) formulation, where particle velocities are directly transferred to the grid without storing affine velocity fields.

{{imp}}{imp:lec29:p2g}[PIC Particle-to-Grid (P2G) Transfers, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:p2g}}
```

Right after Particle-to-Grid (P2G) Transfer, we normalize grid momentum to obtain nodal velocities and enforces Dirichlet boundary conditions near the domain edges by zeroing out velocities.

{{imp}}{imp:lec29:grid_update}[Grid Update, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:grid_update}}
```

During the grid-to-particle (G2P) transfer, we gather the updated velocity from the background grid and compute the elastic deformation gradient update using the velocity gradient derived from the interpolation function.

{{imp}}{imp:lec29:g2p}[PIC Grid-to-Particle (G2P) Transfers, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:g2p}}
```

Finally, particle positions are updated through advection using symplectic Euler time integration.

{{imp}}{imp:lec29:particle_update}[Particle State Update, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:particle_update}}
```

A full MPM simulation step consists of the following stages:

{{imp}}{imp:lec29:time_step}[A full time step of MPM, simulator.py]
```python
{{#include solid-sim-tutorial/10_mpm_elasticity/simulator.py:time_step}}
```

<figure>
    <center>
    <img src="img/lec29/mpm_elastic_blocks_simulation_result.png">
    </center>
    <figcaption><b>{{fig}}{fig:lec29:mpm_elastic_blocks_simulation_result}</b> <b>Time sequence of two colliding elastic blocks in 2D</b>. The red and blue blocks approach each other with opposite velocities, undergo large elastic deformation upon impact, and rebound with shape recovery. The simulation demonstrates symmetric momentum exchange and elastic energy storage under the MPM framework. </figcaption>
</figure>