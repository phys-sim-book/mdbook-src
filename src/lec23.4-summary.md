## Summary

In this section, we delve into the process of extending our solid simulator to accommodate 3D elastodynamic simulation.

This enhancement involves discretizing the solid domain using 3-simplex tetrahedral elements. Consequently, the kinematics, mass matrix, and elasticity energies adopt the same approach as in 2D, but now incorporate an additional dimension for the per-element parameter space, integration, and deformation gradient.

To maintain inversion-free elements, line search filtering operates similarly, though it now entails solving cubic equations for each element.

In the following section, we will explore the extension of the frictional contact component to 3D scenarios.
