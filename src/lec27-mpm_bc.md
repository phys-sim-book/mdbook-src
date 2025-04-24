## Boundary Treatments

During the grid update step, we apply external forces and solve for nodal accelerations and velocities. To ensure physical plausibility and numerical stability, **boundary conditions** must also be enforced at this stage.

In this lecture, we describe how boundary treatments are implemented in MPM. Specifically:

- [Section 27.1](./lec27.1-grid_bc.md) explains how velocity constraints and external forces are imposed directly on grid nodes to handle **Dirichlet** and **Neumann** conditions.
- [Section 27.2](./lec27.2-contact_on_particles.md) focuses on more accurate **contact and friction** treatments, which are handled at the **particle**, enabling flexible and accurate interaction with complex boundaries.

These treatments are essential for producing realistic contact behavior—such as sticking, sliding, and separation—that adheres to the Coulomb friction law.