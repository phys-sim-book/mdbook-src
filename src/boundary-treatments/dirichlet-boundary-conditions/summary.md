## Summary

In this section, we explored Dirichlet boundary conditions (DBC), integral to optimization time integrators, and presented them as straightforward linear equality constraints. There are two types of DBCs: **sticky** and **slip**. Sticky DBCs immobilize certain nodes, fixing their positions, whereas slip DBCs restrict the movement of nodes to within a plane or a line.

We focused on cases where sticky DBCs are already met at the start of a time step. In such scenarios, the **DOF elimination method** proves efficient. This technique modifies the gradient and Hessian of the Incremental Potential, ensuring that the resulting search direction remains within the feasible space.

In the following lecture, we will delve into the handling of slip DBCs and demonstrate methods for their efficient incorporation into optimization problems.
