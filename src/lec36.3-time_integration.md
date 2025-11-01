
### Time Integration as an Optimization Problem

With a discrete potential energy $E^{total}(\mathbf{x}, \mathbf{q})$ from {{eqref:eq:discrete:total_energy_scalar}}, how do we simulate the rod's motion? As established in Chapter 2, an implicit time integration scheme can be formulated as an optimization problem. The state of our system at the next time step is the one that minimizes the variational energy:
$$
{{numeq}}{eq:time_integration:variational_energy}
(\mathbf{x}, \mathbf{q}) = \arg\min_{\mathbf{x},\mathbf{q}} \frac{1}{2h^2}\|\mathbf{x}-\mathbf{y}\|_M^2 + \frac{1}{2h^2}\|\mathbf{q}-\mathbf{w}\|_J^2 + E^{total}(\mathbf{x},\mathbf{q})
$$
subject to the constraint that for all $i$, $\|\mathbf{q}_i\| = 1$.

The first two terms represent the kinetic energy for the positions and orientations, respectively. However, we are immediately faced with a significant challenge: the combination of a complex, coupled system and the non-linear **unit quaternion constraint** on every orientation.

#### Case Study: An XPBD Solver

So how is such an optimization problem solved in practice? One popular approach, especially for interactive applications, is to use the **Extended Position-Based Dynamics (XPBD)** framework, which we have seen in previous chapters. The goal is to solve the variational energy minimization by treating the potential energy terms as constraints to be satisfied. The XPBD optimization problem at each time step is:
$$
{{numeq}}{eq:time_integration:xpbd_optimization}
\arg\min \sum_{p} \frac{m_p}{2\Delta t^2} \|\mathbf{x}_p - \mathbf{y}_p\|^2 + \sum_{s} \frac{1}{2\Delta t^2} \|\mathbf{q}_s - \mathbf{w}_s\|_{J_s}^2 + \sum_s E_s^{ss} + \sum_s E_s^{bt}
$$
To solve this, XPBD iteratively computes updates to the Lagrange multipliers ($\Delta\lambda$) for each constraint. The general update rule for a constraint $C(\mathbf{x})$ is:
$$
{{numeq}}{eq:time_integration:xpbd_update_rule}
\Delta\lambda = \frac{-(C(\mathbf{x}) + \tilde{\alpha}\lambda_{\text{old}})}{\nabla C^T M^{-1} \nabla C + \tilde{\alpha}}
$$
Applying this to our specific rod constraints gives the following updates:

For the **stretch/shear constraint**, $\mathbf{C}_s^{ss} = \frac{\mathbf{x}_{p+1} - \mathbf{x}_p}{l_0} - \mathbf{d}_{s,3}$ (which corresponds to {{eqref:eq:discrete:stretch_strain}}), the update for its Lagrange multiplier is:
$$
{{numeq}}{eq:time_integration:stretch_update}
\Delta\lambda^{ss} = \frac{-(\mathbf{C}_s^{ss} + \tilde{\alpha}^{ss}\lambda^{ss})}{w_{p} + w_{p+1} + 4l_0^2 w_s + \tilde{\alpha}^{ss}}
$$
And for the **bend/twist constraint** (related to {{eqref:eq:discrete:bend_twist_strain}}), the update is {{#cite hsu2023sag}}:
$$
{{numeq}}{eq:time_integration:bend_twist_update}
\Delta\lambda^{bt} = \frac{-(\overline{\mathbf{q}}_{s}\mathbf{q}_{s_{+}} - \phi\boldsymbol{\Omega}^{0}) - \tilde{\alpha}^{bt}\lambda^{bt}}{w_{s} + w_{s_{+}} + \tilde{\alpha}^{bt}}
$$
Notice that this update enforces a constraint on the *full* quaternion product, not just its imaginary part. As we discussed in the previous section, this corresponds to using the augmented Darboux vector, which resolves ambiguities and leads to more stable behavior {{#cite hsu2023sag}}.

As a reminder, in these formulas, $\lambda$ is the Lagrange multiplier, $\tilde{\alpha}$ is the compliance (related to inverse stiffness), and $w$ represents the inverse mass or inverse inertia tensor. The term $\phi$ is a sign factor used to ensure the update always moves toward the closest valid rest pose, resolving the ambiguity caused by the fact that $\mathbf{q}$ and $-\mathbf{q}$ represent the same rotation {{#cite hsu2023sag}}. 