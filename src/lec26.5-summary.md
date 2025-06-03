## Summary

A single time step of the Material Point Method (MPM) simulation proceeds as follows:

1. **P2G (Particle to Grid)**: Transfer particle mass, momentum, and force (computed using stresses) to grid nodes using interpolation functions.
2. **Grid Update**: Update nodal velocities via time integration while enforcing boundary conditions.
3. **G2P (Grid to Particle)**: Interpolate updated grid velocities back to particles; optionally reconstruct affine velocity fields (APIC/MLS-MPM).
4. **Particle State Update**: Update each particle’s deformation gradient based on local velocity gradients and advect particle positions using updated velocities.

This hybrid particle-grid framework enables convenient and effective handling of large deformations and topological changes.

In the following section, we will explore how MPM naturally supports the simulation of plastic deformation by incorporating a return mapping procedure based on the material’s yield criterion.