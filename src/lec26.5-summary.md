## Summary

A single time step of the Material Point Method (MPM) simulation proceeds as follows:

1. **P2G (Particle to Grid)**: Transfer particle mass, momentum, and stress to grid nodes using interpolation functions.
2. **Grid Solve**: Compute forces on the grid and update nodal velocities; also enforce boundary conditions.
3. **G2P (Grid to Particle)**: Interpolate updated grid velocities back to particles; optionally reconstruct affine velocity fields (APIC/MLS-MPM).
4. **Particle State Update**: Update each particle’s deformation gradient based on local velocity gradients and advect particle positions using updated velocities.

This hybrid particle-grid framework enables stable handling of large deformations and topological changes.

In the following section, we will explore how MPM’s scheme naturally supports the simulation of plastic deformation by incorporating a return mapping procedure based on the material’s yield criterion.