## Particle-Grid Transfers

In MPM, the material state is stored on **Lagrangian particles**, while the [Governing Equations](./lec16-strong_and_weak_forms.md) (e.g., conservation of momentum) are solved on a temporary **Eulerian grid**. Therefore, we must transfer information between particles and grid nodes at each time step.

This section discusses how **mass**, **momentum**, and **internal forces (stress)** are transferred from particles to grid (P2G), and how updated velocities are transferred back from grid to particles (G2P).



### Particle-to-Grid (P2G) Transfers

**Mass Transfer**: Let $m_p$ be the mass of particle $p$, and $w_{\mathbf{i}p} = N_\mathbf{i}(\mathbf{x}_p)$ be the [interpolation weight](./lec26.2-interpolating_functions.md) between grid node $\mathbf{i}$ and particle $p$. We transfer mass to grid node $\mathbf{i}$ by taking a weighted sum:

$$
m_\mathbf{i} = \sum_p w_{\mathbf{i}p} \, m_p.
$$

**Momentum Transfer**: Similarly, particle velocity $\mathbf{v}_p$ is used to compute nodal momentum:

$$
(m \mathbf{v})_\mathbf{i} = \sum_p w_{\mathbf{i}p} \, m_p \mathbf{v}_p.
$$

Then, the **nodal velocity** of each grid node is:

$$
\mathbf{v}_\mathbf{i} = \frac{(m \mathbf{v})_\mathbf{i}}{m_\mathbf{i}}.
$$

**Internal Force (Stress) Transfer**: In MPM, forces are evaluated on grid nodes where [Newton’s second law](./lec1.2-newton_2nd_law.md) is applied. For hyperelastic materials, the internal elastic force can be derived either from the [Weak Form](./lec16.3-weak_form.md) of the momentum equation or from the gradient of the [Strain Energy](./lec13-strain_energy.md). The latter is often preferred when a well-defined energy density function is available.

Assuming a deformation-gradient-based hyperelastic energy density $\Psi_p(\mathbf{F}_p)$ at each particle $p$, the total potential energy of the system is:

$$
\mathcal{E} = \sum_p V_p^0 \, \Psi_p(\mathbf{F}_p),
$$

where $V_p^0$ is the rest volume of particle $p$. The elastic force acting on grid node $i$ is then computed as the negative gradient of the total energy with respect to the nodal position $\mathbf{x}_\mathbf{i}$:

$$
\mathbf{f}_\mathbf{i}(\mathbf{x}) = - \frac{\partial \mathcal{E}}{\partial \mathbf{x}_\mathbf{i}} = - \sum_p V_p^0 \, \frac{\partial \Psi_p}{\partial \mathbf{F}}(\mathbf{F}_p(\mathbf{x})) \, \mathbf{F}_p^T \, \nabla w_{\mathbf{i}p}.
$$

In the case of explicit time integration such as Symplectic Euler, the force is evaluated at the current time step $n$:

$$
\mathbf{f}_\mathbf{i}^n = - \sum_p V_p^0 \, \frac{\partial \Psi_p}{\partial \mathbf{F}}\left(\mathbf{F}_p^n\right) \, (\mathbf{F}_p^n)^T \, \nabla w_{\mathbf{i}p}^n.
$$

This formulation expresses the force on grid node $i$ due to the elastic stress contributions from nearby particles. It depends entirely on known particle attributes and interpolation weights at the current time step.

### Grid Update

After collecting information from the particles, time integration is then performed on the grid, where 1st-order schemes are usually used:

$$
\hat{\mathbf{v}}_\mathbf{i}^{n+1} = \mathbf{v}_\mathbf{i}^n + \frac{\Delta t}{m_\mathbf{i}} (\mathbf{f}_\mathbf{i} + \mathbf{f}_\mathbf{i}^{\text{ext}}).
$$
Here, we use $\hat{\mathbf{v}}_\mathbf{i}^{n+1}$ to represent velocity after grid update, so that it differs from the grid velocity after P2G transfer in the next time step, which is written as $\mathbf{v}_\mathbf{i}^{n+1}$.

[Boundary treatments](./lec28-mpm_bc.md) are also enforced during this stage and will be discussed in later lectures.

### Grid-to-Particle (G2P) Transfers

Once the grid velocity $\hat{\mathbf{v}}_\mathbf{i}^{n+1}$ is updated, we need to transfer information back to the particles. The main quantity to update during the G2P transfer is the particle velocity $\mathbf{v}_p^{n+1}$, which will be used in the [advection](src/lec26.4-particle_state_update.md) step.

The simplest approach is to interpolate velocity directly from the grid:

$$
\mathbf{v}_p^{n+1} = \sum_\mathbf{i} w_{\mathbf{i}p} \, \hat{\mathbf{v}}_\mathbf{i}^{n+1}.
$$

This straightforward scheme is part of the **Particle-In-Cell (PIC)** {{#cite harlow1962particle}} method, but it is highly dissipative. In practice, we often adopt improved transfer schemes to better preserve velocity modes and conserve angular momentum.

### Different Transfer Schemes

Transfer schemes define how **P2G** and **G2P** collaborate, balancing numerical stability, energy conservation, and physical realism in different ways.

> {{exp}}{exp:lec26:pic}[PIC, Particle-In-Cell] $$ $$
**P2G**: Particle velocity $\mathbf{v}_p$ is transferred to the grid using:
$$
(m\mathbf{v})_\mathbf{i} = \sum_p w_{\mathbf{i}p} \, m_p \mathbf{v}_p.
$$
**G2P**: Particle velocity is interpolated directly from the grid:
$$
\mathbf{v}_p^{n+1} = \sum_\mathbf{i} w_{\mathbf{i}p} \, \hat{\mathbf{v}}_\mathbf{i}^{n+1}.
$$
PIC is very stable and conserves linear momentum, but suffers from high numerical dissipation, loss of fine-scale motion, and does not conserve angular momentum.

> {{exp}}{exp:lec26:flip}[FLIP, Fluid-Implicit-Particle] $$ $$
**P2G**: Same as PIC. $$ $$
**G2P**: In FLIP, the particle velocity is updated by blending the newly interpolated velocity and the change in velocity between time steps:
$$
\mathbf{v}_p^{n+1} =
\sum_\mathbf{i} w_{\mathbf{i}p} \, \hat{\mathbf{v}}_\mathbf{i}^{n+1}
+ \alpha \left( \mathbf{v}_p^n - \sum_\mathbf{i} w_{\mathbf{i}p} \, \mathbf{v}_\mathbf{i}^n \right),
$$
where $\alpha \in [0, 1]$ is the **FLIP-PIC blending factor** (with $\alpha = 1$ recovering full FLIP and $\alpha = 0.99$ often used). Since FLIP only interpolate the velocity change, numerical dissipation is effectively mitigated. $$ $$
However, note that to ensure the embedding relation between the particle and grid, particle positions still need to be updated using PIC's velocity:
$$
\mathbf{x}_p^{n+1} = \mathbf{x}_p^n + \Delta t \sum_\mathbf{i} w_{\mathbf{i}p} \, \hat{\mathbf{v}}_\mathbf{i}^{n+1}.
$$
FLIP can preserve system energy and fine-scale motion over long time scales, making it well-suited for simulating fluids with detailed local features. However, due to its lack of inherent smoothing, it can introduce noise, penetration, or even instability in solid simulations.

> {{exp}}{exp:lec26:apic}[APIC, Affine Particle-in-Cell] $$ $$
@@jiang2015affine introduces **affine velocity fields** to more accurately model local particle motion and preserve angular momentum. Each particle stores an affine velocity matrix $\mathbf{C}_p$ in addition to its velocity $\mathbf{v}_p$. The particle velocity field is written as:
$$
\mathbf{v}_p(\mathbf{x}) = \mathbf{v}_p + \mathbf{C}_p (\mathbf{x} - \mathbf{x}_p).
$$
**P2G**: The momentum transfer includes affine motion:
$$
(m\mathbf{v})_\mathbf{i} = \sum_p w_{\mathbf{i}p} \, m_p \left( \mathbf{v}_p + \mathbf{C}_p (\mathbf{x}_\mathbf{i} - \mathbf{x}_p) \right).
$$
This enables particles to transfer both linear and angular momentum to the grid. $$ $$
**G2P**: After the grid update, particle velocity and affine matrix are reconstructed from grid values:
$$
\mathbf{v}_p^{n+1} = \sum_\mathbf{i} w_{\mathbf{i}p} \, \hat{\mathbf{v}}_\mathbf{i}^{n+1},
$$
$$
\mathbf{B}_p^{n+1} = \mathbf{D}_p^{-1} \sum_\mathbf{i} w_{\mathbf{i}p} \, \hat{\mathbf{v}}_\mathbf{i}^{n+1} (\mathbf{x}_\mathbf{i} - \mathbf{x}_p)^T.
$$
Here, $\mathbf{D}_p$ is a second moment matrix defined as:
$$
\mathbf{D}_p = \sum_\mathbf{i} w_{\mathbf{i}p} (\mathbf{x}_\mathbf{i} - \mathbf{x}_p)(\mathbf{x}_\mathbf{i} - \mathbf{x}_p)^T.
$$
For common B-spline interpolation kernels (quadratic and cubic), $\mathbf{D}_p$ has a constant value: $$ $$
 **Quadratic B-spline**: $\mathbf{D}_p = \frac{1}{4} \Delta x^2 \mathbf{I}$. $$ $$
 **Cubic B-spline**: $\mathbf{D}_p = \frac{1}{3} \Delta x^2 \mathbf{I}$. $$ $$
Thus, $\mathbf{D}_p^{-1}$ is simply a constant scaling factor and need not be recomputed per particle. $$ $$
APIC significantly reduces dissipation compared to PIC by capturing local rotational and shear effects through affine velocity fields, while also preserving angular momentum. Although it requires storing an additional local matrix $\mathbf{C}_p$ per particle and computing particle-to-grid moment contributions, the cost is modest in practice.