## Boundary Conditions on Grid

In the Material Point Method, **boundary conditions (BCs)** are fundamentally enforced on the background Eulerian grid, because the governing equations—discretized in their weak form—are formulated over grid nodes, which serve as the true degrees of freedom (DOFs) of the system. These nodes must satisfy Newton's Second Law. Thus, all external constraints, including static walls, moving boundaries, or contact forces, must be applied directly on the grid after P2G and before G2P.

We denote the tentative nodal velocity at a grid node $\mathbf{x}_i$ after external force integration as $\hat{\mathbf{v}}_i^{n+1}$. Depending on the type of boundary condition, we apply different velocity projections.

### Common Grid Boundary Conditions

Let $\mathbf{n}$ be the surface normal at a boundary grid node $\mathbf{x}_i$:

- **Sticky (Dirichlet)**: The velocity is fully suppressed:
  $$
  \mathbf{v}_i^{n+1} = \mathbf{0}
  $$

- **Slip (Neumann)**: Only the normal component is removed; tangential motion is preserved:
  $$
  \mathbf{v}_i^{n+1} = \hat{\mathbf{v}}_i^{n+1} - \mathbf{n}(\mathbf{n}^\top \hat{\mathbf{v}}_i^{n+1})
  $$

- **Separate (One-way wall)**: Normal inflow is blocked, but outward motion is allowed:
  $$
  \mathbf{v}_i^{n+1} = \hat{\mathbf{v}}_i^{n+1} - \mathbf{n} \cdot \min(\mathbf{n}^\top \hat{\mathbf{v}}_i^{n+1}, 0)
  $$

These operations are **local**, applied independently to each boundary grid node.

### Adding External Forces

External forces such as **gravity** can be added after P2G:

$$
\hat{\mathbf{v}}_i^{n+1} \mathrel{+}= \Delta t \cdot \mathbf{g}
$$

### Moving Colliders and Signed Distance Fields

For moving or deforming objects, [**Signed Distance Functions (SDFs)**](./lec7.1-signed_dists.md) are used to detect whether a grid node $\mathbf{x}_i$ lies inside the object. Once detected, the **surface normal** $\mathbf{n}$ and the **relative velocity** $\Delta \mathbf{v}$ are computed, and the grid velocity is adjusted accordingly.

### Coulomb Friction Projection

To enforce **Coulomb friction** on embedded colliders (e.g., sphere or capsule), we apply a projected velocity correction in the direction of the surface normal:

Let $\mathbf{n}$ be the contact normal and $\Delta \mathbf{v}$ be the opposing velocity (i.e., $\Delta \mathbf{v} = \mathbf{v}_{\text{SDF}} -\hat{\mathbf{v}}_i^{n+1}$). Define the normal component:

$$
\dot{v}_n = \mathbf{n}^\top \Delta \mathbf{v}
$$

Then the corrected grid velocity is:

$$
\mathbf{v}_i^{n+1} = \hat{\mathbf{v}}_i^{n+1} + \Delta \mathbf{v} \cdot \mu + \mathbf{n} \cdot \dot{v}_n \cdot (1 - \mu)
$$

where $\mu \in [0, 1]$ is the **friction coefficient**. This operation smoothly blends between full **separation** ($\mu = 0$) and **sticking** ($\mu = 1$).