# Position Based Dynamics Framework
**Author of this lecture: [Žiga Kovačič](https://zzigak.github.io), Cornell University*

This section introduces Position Based Dynamics (PBD). In the field of physical simulation, several dominant paradigms exist, each with a different level of abstraction for describing motion. 

The most classical approach is force-based, which directly models Newton's second law. In a force-based paradigm, internal and external forces are accumulated to determine particle accelerations, which are then numerically integrated to find new velocities and positions. While physically accurate, these methods can suffer from expensive computational costs due to the solution of large nonlinear systems. 

Position-based methods bypass the velocity and acceleration layers altogether, working immediately on the positions of particles. The central idea is to define the system's behavior through a set of geometric constraints. The simulation loop first predicts a new position for each particle based on its current velocity. Then, an iterative solver adjusts these predicted positions directly to ensure that all constraints are satisfied. This process of constraint projection replaces the explicit integration of forces, leading to highly efficient and visually plausible simulations, which makes PBD an effective alternative to classical dynamics frameworks.

This section is inspired by incedible notes on PBD by {{#cite bender2017survey}} and {{#cite macklin2013position}}.