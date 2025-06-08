## Frictional Contact on Material Particles

Previously, we discussed applying boundary conditions directly on the Eulerian grid nodes. While this approach is natural due to the grid nodes being the true degrees of freedom (DOFs) where Newton's second law is enforced, it suffers from several inherent issues:

- **Normal Estimation Error**: For coarse grids, estimating accurate surface normals becomes challenging, potentially resulting in incorrect contact responses.
- **Contact gap (dx gap)**: Grid-based collision handling responds even when grid nodes are within approximately one grid cell spacing from collision surfaces, leading to visible gaps and earlier collision responses.

Given that the Material Point Method (MPM) discretizes solids directly into a set of material particles, it is more intuitive—and potentially more accurate—to handle frictional contact directly at the particle level. However, it is not physically consistent to simply compute collision forces and friction at particles and then directly transfer them (via P2G) to the grid, because this naïve approach bypasses the correct enforcement of Newton’s second law on the grid DOFs, potentially resulting in unphysical responses, particularly failing to properly enforce static friction constraints.

Instead, a more robust strategy is to formulate the frictional contact problem at the grid level explicitly as an optimization problem, taking advantage of the particle-based definition of collision energies or penalty functions. This leads us to adopt a two-stage time-splitting numerical scheme.

### Two-Stage Time-splitting Optimization Scheme

**Stage 1: Free-motion velocity**

In the first stage, we ignore contact constraints entirely and compute the intermediate "free-motion" nodal velocity $\mathbf{v}_i^*$ on grid nodes, which would occur in the absence of any contact forces. These velocities can be conveniently obtained via a standard [symplectic Euler](./lec26.3-euler_lag_transfers.md)  time integration step.

**Stage 2: Frictional Contact via Optimization**

In the second stage, frictional contact is enforced by solving a constrained linearized momentum balance around the free-motion velocity $\mathbf{v}^*$:

$$
\mathbf{M}(\mathbf{v}^{n+1}_i-\mathbf{v}^*_i) = h \mathbf{J}^T(\mathbf{x}^n_p)\gamma(\mathbf{v}^{n+1}_i).
$$

Here:

- $\mathbf{M}$ is the mass matrix.
- $\mathbf{J}$ is the contact Jacobian defined at the particle level, it maps grid nodal velocities to particle-level contact velocities. Specifically, each contacting particle $ j $ contributes a local Jacobian matrix:
$$
\mathbf{J}_j = \frac{\partial \mathbf{v}_{c,j}}{\partial \mathbf{v}_i} \in \mathbb{R}^{3\times n_v}.
$$
Stacking all these individual Jacobians produces the global contact Jacobian $ \mathbf{J} \in \mathbb{R}^{3 n_c\times n_v} $, which collectively relates all particle contact velocities $ \mathbf{v}_c $ to grid velocities $ \mathbf{v} $ as:
$$
\mathbf{v}_c = \mathbf{J}\mathbf{v}_i.
$$
Intuitively, $ \mathbf{J} $ can be understood as a special Grid-to-Particle (G2P) transfer operator linearized at the current configuration, directly encoding how infinitesimal changes in grid nodal velocities affect the particle velocities (normal and tangential components) at contact points.

- $\gamma(\mathbf{v}^{n+1}_i)$ represents contact forces (normal and tangential) subject to friction cone constraints.

This linear system can be reformulated into an **unconstrained optimization problem**:

$$
\min_{\mathbf{v}_i}\, \ell(\mathbf{v}_i) = \frac{1}{2}\|\mathbf{v}_i-\mathbf{v}^*_i\|_{\mathbf{M}}^2 + \ell_c(\mathbf{v}_c(\mathbf{v}_i)).
$$

Possible contact energy formulations for $\ell_c$ include the [Logarithm Barrier Potential](./lec7.2-dist_barrier_formulation.md) and [Semi-Implicit Friction](./lec9.2-semi_imp_fric.md).