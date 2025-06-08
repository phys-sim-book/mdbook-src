## Deformation Gradient and Particle State Update

In MPM, each particle carries a deformation gradient $\mathbf{F}_p$, which tracks how the local material volume attached to the particle is stretched, rotated, or sheared over time. It plays a central role in computing stress based on constitutive models.

At the beginning of each time step, the grid lies on a regular, undeformed lattice. Let $\mathbf{x}_i^n$ be the position of grid node $i$ at time $t^n$. The deformation gradient is updated by observing how the grid locally moves over the time step, assuming a given grid velocity field $\hat{\mathbf{v}}_i^{n+1}$.

### Deformation Gradient Update

The most common approach is to compute $\mathbf{F}_p^{n+1}$ using a 1st-order approximation:

$$
\mathbf{F}_p^{n+1} =
\left( \mathbf{I} + \Delta t \sum_i \mathbf{v}_i^{n+1} \nabla (w_{ip}^n)^T \right) \mathbf{F}_p^n.
$$

In **MLS-MPM** @@hu2018moving and **APIC** @@jiang2015affine, the deformation gradient update can be rewritten more compactly using the affine velocity matrix $\mathbf{C}_p$ that is already computed during P2G and G2P transfers.

Recall the local affine velocity field:

$$
\mathbf{v}_p(\mathbf{x}) = \mathbf{v}_p + \mathbf{C}_p (\mathbf{x} - \mathbf{x}_p).
$$

From this, the grid motion induces a local velocity gradient $\mathbf{C}_p$, and the deformation gradient can be updated as:

$$
\mathbf{F}_p^{n+1} = \left( \mathbf{I} + \Delta t \, \mathbf{C}_p \right) \mathbf{F}_p^n.
\tag{3}
$$

This bypasses the need to explicitly evaluate $\nabla w_{ip}$, and instead uses the already-aggregated affine behavior stored in $\mathbf{C}_p$.

**Plasticity flow** is also applied at this stage to project the updated deformation gradient back onto the admissible space defined by the material's yield criterion. This process, known as **return mapping**, ensures that the material obeys plastic limits, and will be discussed in detail in the [next lecture](./lec27-mpm_plasticity.md).

### Position Update

The particle position at time $t^{n+1}$ is then advected as:

$$
\mathbf{x}_p^{n+1} = \mathbf{x}_p^{n} + \Delta t \mathbf{v}_p^{n+1}.
$$