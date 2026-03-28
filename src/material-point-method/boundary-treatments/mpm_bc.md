# Boundary Treatments

**Author of this lecture: [Chang Yu](https://changyu.io/), University of California, Los Angeles*

During the grid update step, we apply external forces and solve for nodal accelerations and velocities. To model object interactions with the physcal environment, **boundary conditions** can be enforced at this stage.

In this lecture, we describe how boundary treatments are implemented in MPM. Specifically:

- [Boundary Conditions on Grid](./lec28.1-grid_bc.md) explains how velocity constraints and external forces are imposed directly on grid nodes to handle **Dirichlet** and **Neumann** conditions.
- [Frictional Contact on Material Particles](./lec28.2-contact_on_particles.md) focuses on more accurate **contact and friction** treatments, which are handled at the **particle**, enabling flexible and more accurate interaction with complex boundaries.

These treatments are essential for producing realistic contact behavior—such as sticking, sliding, and separation—that adheres to the Coulomb friction law.