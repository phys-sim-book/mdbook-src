# 3D Elastodynamics

To extend our 2D solid simulator ([2D Frictional Self-Contact](./lec22-2d_self_fric.md)) to 3D, we can use $3$-simplex tetrahedral elements to discretize the 3D solid domains. In this approach, the surface of the solid is represented as a triangle mesh, which is a common method in computer graphics for representing 3D geometries. Additionally, we need to sample vertices in the interior of the solid to form the tetrahedral elements required for discretizing the inertia and elasticity energies.
