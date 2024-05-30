## Penalty Method

At the beginning of each time step towards time $n+1$, we evaluate nodal position $\hat{\mathbf{x}}_k^{n+1}$ for each BC node $k$ based on their prescribed motions. During each Newton iteration $i$, for the iterate $x^i$, we define a velocity residual to assess how close each BC node is to meeting its target:
$$
r^i_{\text{BC}, k} = \frac{1}{h} \|\mathbf{x}^i_k - \hat{\mathbf{x}}^{n+1}_k\|.
$$
When $r^i_{\text{BC}, k}$ falls below a specific tolerance $\epsilon$ for any BC node $k$, we can fix the node at its current location $\mathbf{x}^i_k \approx \hat{\mathbf{x}}^{n+1}_k$ and apply the [DOF elimination method](lec5.2-DOF_elimin.md) in the subsequent iterations. This is particularly straightforward in scenes with only static BCs, where the DOF elimination method is directly applied.

For other BC nodes $k$ that are far from their target locations, we introduce new penalty terms to the Incremental Potential for each of these nodes:
$$
\frac{\kappa_\text{M}}{2}m_k \|\mathbf{x}_k - \hat{\mathbf{x}}_k^{n+1}\|^2.
{{numeq}}{eq:lec11:DBC_penalty}
$$
Here, $m_k$ represents the nodal mass, allowing for intuitive setting of the penalty stiffness $\kappa_\text{M}$, as the Hessian of the penalty term with respect to BC nodes is simply $\kappa_\text{M}$ times that of the inertia term.

> **{{rem}}{rm:lec11:CO_penalty}**
> For collision obstacles (CO), precisely calculating node masses is challenging due to unknown factors like density. A practical approach is to assume a density similar to that of the simulated solids in the scene. This assumption makes the diagonal entries on the Hessian of the penalty terms roughly $\kappa_\text{M}$ times that of the inertia term.
>
> For codimensional COs such as shells, rods, and particles, the key is to consider a reasonably large thickness when calculating their volumes. This helps in ensuring that their physical properties align more closely with those of the main simulation bodies.

Setting the penalty stiffness $\kappa_\text{M}$ appropriately can be challenging. If $\kappa_\text{M}$ is set too low, it may not effectively move the BC node towards its target. Conversely, a too high $\kappa_\text{M}$ can lead to numerical issues. Thus, we initially set $\kappa_\text{M}$ to a reasonably large value and adaptively increase it as necessary.

During the Newton solve, if there are BC nodes $k$ where $r^i_{\text{BC}, k} \geq \epsilon$ at the point of Newton convergence, we double the penalty stiffness $\kappa_\text{M}$ to $2\times$ its current value and continue the Newton solve. This process is repeated until all BCs are satisfactorily met at convergence.

> **{{rem}}{rm:lec11:numerical_issues}**
> In practice, with double precision floating-point numbers,  initializing $\kappa_\text{M}$ below $10^6$ is typically sufficient, given that the Hessian of the stiff penalty terms is purely diagonal. However, if certain BCs remain unsatisfied even when $\kappa_\text{M}$ is increased to above $10^{10}$, the optimization process may stall due to severe numerical errors. This stalling occurs because extremely stiff penalty terms are in conflict with the contact barriers. However, such a scenario would likely only occur under a rare CO/BC setting in a manner far more extreme than what is tested in {{ref: fig:lec2:contact_and_friction}}. 