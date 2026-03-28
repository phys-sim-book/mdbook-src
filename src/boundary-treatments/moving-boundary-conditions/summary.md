## Summary

We introduced the penalty method for handling moving boundary conditions while preventing interpenetrations. The key strategies involved are:
- Augmenting the Incremental Potential with additional spring energies on the DBC nodes;
- Adaptively increasing the penalty stiffness as required;
- Eliminating DOFs for those BC nodes that are sufficiently close to their targets; and
- Ensuring all BCs are satisfied at the point of convergence.

To address the inversion artifact observed in our case study of compressing mass-spring elastic squares, the application of barrier-type elasticity energies is essential. Our penalty method for moving BCs plays a crucial role when these energies are applied, as directly prescribing BC nodes can still lead to inversion. In the next chapter, we will explore hyperelasticity models, which are preferred over mass-spring systems in practical applications.