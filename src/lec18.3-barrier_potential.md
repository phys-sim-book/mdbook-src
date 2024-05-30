## Barrier Potential

As discussed in [Distance Barrier for Nonpenetration](./lec7-dist_barrier.md), the principle of IPC for solid-to-obstacle contact is to use a barrier function to ensure that the signed distance between any nodal degrees of freedom (DOFs) and obstacles remains positive throughout the simulation. To handle self-contact, potentially for codimensional objects, this idea is extended to ensure that the unsigned distance between any boundary points and the boundary remains nonzero throughout the simulation.

Let's consider two colliding regions, $\Gamma_1 \subset \partial \Omega^0$ and $\Gamma_2 \subset \partial \Omega^0$, on the boundary. For any point $\mathbf{X}_1 \in \Gamma_1$, we must ensure that the closest distance between $\mathbf{X}_1$ and any point on $\Gamma_2$ remains nonzero. This can be achieved by using a barrier function to enforce this minimum distance, where the negative gradient of the function provides the contact force. This can be written as

$$
\mathbf{T}_C(\mathbf{X}_1, t) = -\frac{\partial b(\min_{\mathbf{X}_2 \in \Gamma_2} \|\mathbf{x}(\mathbf{X}_1, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d})}{\partial \mathbf{x}(\mathbf{X}_1, t)},
{{numeq}}{eq:lec18:solid_solid_contact_force}
$$

where $b$ is the barrier function:

$$
b(d, \hat{d}) = 
\begin{cases}
    \frac{\kappa}{2} \hat{d} \left(\frac{d}{\hat{d}} - 1\right) \ln \frac{d}{\hat{d}} & d < \hat{d} \\
    0 & d \geq \hat{d},
\end{cases}
{{numeq}}{eq:lec18:barrier_func}
$$

serving as the contact potential energy density. Here, the barrier function approaches infinity as the distance approaches zero, providing an arbitrarily large repulsive force to prevent interpenetration. When the distance is above the threshold $\hat{d}$ and no contact is occurring, no contact forces are exerted. By using the barrier function, the non-smooth contact constraints are approximated by a constitutive model in which the force is conservative, enabling consistent resolution through an optimization-based time integrator.

> **{{rem}}{rem:lec18:barrier_density}[Barrier Density]**
> Compared to Equation {{eqref: eq:lec7:barrier_func}}, the barrier energy density function here is additionally multiplied by $\hat{d}$ to maintain consistent units after surface integration. Recall from {{ref: rm:lec7:contact_layer}} that the barrier potential can be thought of as an extra thin layer of ($\hat{d}$-thick) virtual material right outside the boundary of the solids, and $\kappa$ is analogous to Young's modulus.

> **{{rem}}{rem:lec18:min_operator}[Min Operator]**
> When multiple points have the same minimal distance to $\mathbf{X}_1$, the distance barrier of $\mathbf{X}_1$ to all these points should be summed up. The `min` operator is non-smooth, which can still complicate optimization-based time integration. In the next chapter, we will demonstrate how this is approximated as described in [Distance Barrier for Nonpenetration](./lec7-dist_barrier.md).

The case of two colliding regions results in a boundary integral:

$$
\int_{\Gamma_1}\mathbf{Q}_i(\mathbf{X}_1,t)\mathbf{T}_{C1|i}(\mathbf{X}_1,t)\,ds(\mathbf{X}) + \int_{\Gamma_2}\mathbf{Q}_i(\mathbf{X}_2,t)\mathbf{T}_{C2|i}(\mathbf{X}_2,t)\,ds(\mathbf{X}),
$$

where $\mathbf{T}_{C1}$ is defined in Equation {{eqref: eq:lec18:solid_solid_contact_force}}, and:

$$
\mathbf{T}_{C2}(\mathbf{X}_2, t) = -\frac{\partial b(\min_{\mathbf{X}_1 \in \Gamma_1} \|\mathbf{x}(\mathbf{X}_2, t) - \mathbf{x}(\mathbf{X}_1, t)\|, \hat{d})}{\partial \mathbf{x}(\mathbf{X}_2, t)},
{{numeq}}{eq:lec18:solid_solid_contact_force2}
$$

However, we have ignored the self-contact of $\Gamma_1$ and $\Gamma_2$ in this example. Thus, generalizing to arbitrary self-contact for the whole domain, we can keep the single boundary integral term $\int_{\Gamma_C}\mathbf{Q}_i(\mathbf{X},t)\mathbf{T}_{C|i}(\mathbf{X},t)\,ds(\mathbf{X})$ for contact as in Equation {{eqref: eq:lec18:boundary_int_with_contact}} and define the traction more generally as:

$$
\mathbf{T}_C(\mathbf{X}, t) = -\frac{\partial b(\min_{\mathbf{X}_2 \in \Gamma_C - \mathcal{N}(\mathbf{X})} \|\mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d})}{\partial \mathbf{x}(\mathbf{X}, t)},
{{numeq}}{eq:lec18:solid_self_contact_force}
$$

where $\mathcal{N}(\mathbf{X}) = \{ \mathbf{X}_N \in \mathbb{R}^d \mid \|\mathbf{X}_N - \mathbf{X}\| < r \}$ is an infinitesimal circle around $\mathbf{X}$ with the radius $r$ sufficiently small to avoid unnecessary contact forces between a point and its geodesic neighbors.

> **{{rem}}{rem:lec18:barrier_force_limit}[Barrier Force Limits]**
> In Equation {{eqref: eq:lec18:solid_self_contact_force}}, self-contact is ignored for points inside $\mathcal{N}(\mathbf{X})$. This is the trade-off for smoothly approximating contact forces, which are discontinuous in a macroscopic view. Similarly, $\hat{d}$ introduces another source of error. However, when $\hat{d} \rightarrow 0$ and $r \rightarrow 0$, our model converges to the discontinuous definition. Note that we also need $\hat{d}/r \rightarrow 0$, or there could still be some distance $d$ between $r$ and $\hat{d}$ that causes the barrier to diverge in the limit.

Finally, we can define the contact potential over the whole boundary $\Gamma_C$ as:

$$
\int_{\Gamma_C} \frac{1}{2} b(\min_{\mathbf{X}_2 \in \Gamma_C - \mathcal{N}(\mathbf{X})} \|\mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}) \, ds(\mathbf{X}).
{{numeq}}{eq:lec18:barrier_potential}
$$

Here, the coefficient $\frac{1}{2}$ is used because the barrier energy density $b$ of each pair of contacting points will be counted twice in the integral due to symmetry. When computing barrier forces, both occurrences need to be differentiated. Therefore, using the coefficient $\frac{1}{2}$ allows us to match the force definition in Equation {{eqref: eq:lec18:solid_self_contact_force}}. We'll elaborate on this in the next chapter under the discrete weak form.

The contact potential is not required in the weak form but will be useful for optimization-based time integration.
