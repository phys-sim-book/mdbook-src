## Barrier and Distances

With triangle mesh discretization, the barrier potential in the continuous settings (Equation {{eqref: eq:lec18:barrier_potential}}) can be approximated as
$$
\begin{aligned}
    & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{\mathbf{X}_2 \in \Gamma_C - \mathcal{N}(\mathbf{X})} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    \approx & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{e \in \mathcal{T} - I(\mathbf{X})} \min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    = & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{e \in \mathcal{T} - I(\mathbf{X})} d^\text{PT}(\mathbf{x}(\mathbf{X},t), e), \hat{d}\right) ds(\mathbf{X}) \\
    = & \int_{\Gamma_C} \frac{1}{2} \max_{e \in \mathcal{T} - I(\mathbf{X})} b\left(d^\text{PT}(\mathbf{x}(\mathbf{X},t), e), \hat{d}\right) ds(\mathbf{X}),
\end{aligned}
{{numeq}}{eq:lec24:tri_disc_barrier_pot}
$$
where $\mathcal{T}$ is the set of all surface triangles, $I(\mathbf{X})$ is the set of all surface triangles that hold point $\mathbf{X}$, and $d^{\text{PT}}$ is the point-triangle distance.
Further approximating the max operator with summations and use mesh surface nodes $\hat{a}$ as quadrature points, we have
$$
\begin{aligned}
    & \int_{\Gamma_C} \frac{1}{2} \max_{e \in \mathcal{T} - I(\mathbf{X})} b(d^\text{PT}(\mathbf{x}(\mathbf{X},t), e), \hat{d}) ds(\mathbf{X}) \\
    \approx & \int_{\Gamma_C} \frac{1}{2} \sum_{e \in \mathcal{T} - I(\mathbf{X})} b( d^\text{PT}(\mathbf{x}(\mathbf{X},t), e), \hat{d}) ds(\mathbf{X}) \\
    \approx &
    \sum_{\hat{a}} \frac{1}{2} w_{\hat{a}} \sum_{e \in \mathcal{T} - I(\mathbf{X}_{\hat{a}})} b( d^\text{PT}(\mathbf{x}_{\hat{a}}, e), \hat{d}),
\end{aligned}
{{numeq}}{eq:lec23:barrier_discrete}
$$
where $w_{\hat{a}} = \hat{d} \frac{1}{3} \sum_{j\in I(\mathbf{X}_{\hat{a}})} A_j$ is the integration weight and $A_j$ is the area of node $\hat{a}$'s incident surface triangle $j$.

Now, getting back to the second line of Equation {{eqref: eq:lec24:tri_disc_barrier_pot}}, if we only use points on the edges to approximate the minimum distance, we obtain
$$
\begin{aligned}
    & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{\mathbf{X}_2 \in \Gamma_C - \mathcal{N}(\mathbf{X})} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    \approx & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{e \in \mathcal{E} - I(\mathbf{X})} \min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    = & \int_{\Gamma_C} \frac{1}{2} \max_{e \in \mathcal{E} - I(\mathbf{X})} b\left(\min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}).
\end{aligned}
$$
Then if we choose a special quadrature point $\mathbf{X}_{e_1}$ per surface edge $e_1$ and approximate the max operators with summations, we get
$$
\begin{aligned}
    & \int_{\Gamma_C} \frac{1}{2} \max_{e \in \mathcal{E} - I(\mathbf{X})} b\left(\min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    \approx & \sum_{e_1 \in \mathcal{E}} \frac{1}{2} w_{e_1} \sum_{e \in \mathcal{E} - I(\mathbf{X}_{e_1})} b\left(\min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}_{e_1}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right),
\end{aligned}
$$
where $w_{e_1} = \hat{d} \frac{1}{3} \sum_{j\in I(e_1)} A_j$ is the integration weight and $A_j$ is the area of $e_1$'s incident surface triangle $j$.
Next, if we always select $\mathbf{X}_{e_1}$ to be the closest point to $\mathbf{X}_2$ on $e_1$, we will get
$$
\begin{aligned}
    & \sum_{e_1 \in \mathcal{E}} \frac{1}{2} w_{e_1} \sum_{e \in \mathcal{E} - I(\mathbf{X}_{e_1})} b\left(\min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}_{e_1}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) \\
    \approx & \sum_{e_1 \in \mathcal{E}} \frac{1}{2} w_{e_1} \sum_{e \in \mathcal{E} - \mathcal{N}(e_1)} b\left(\min_{\mathbf{X}_2 \in e, \mathbf{X} \in e_1} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) \\
    = & \sum_{e_1 \in \mathcal{E}} \frac{1}{2} w_{e_1}  \sum_{e \in \mathcal{E} - \mathcal{N}(e_1)} b\left(d^{\text{EE}}(e, e_1), \hat{d}\right),
\end{aligned}
{{numeq}}{eq:lec24:edge_barrier}
$$
where $\mathcal{N}(e_1)$ is the set of all the surface edge neighbors of $e_1$ plus itself. For the summation over all surface edges in Equation {{eqref: eq:lec24:edge_barrier}}, if we only account for $(e_1, e)$ with $e_1 < e$ or the other way around, then the coefficient $1/2$ can be omitted.

Now we have two kinds of discretizations for the 3D barrier potential energy. To use them together in practice, we can take advantage of a linear combination of them, and the coefficient could usually be set to $1/2$.

For point-triangle and edge-edge distances, they are also both small optimization problems with analytical solutions, which can be represented as piecewise smooth functions like the 2D Point-Edge distance in Equation {{eqref: eq:lec21:pw_pe_dist}}.
For example, in the point-triangle case, the expression can be determined by checking which region the projection of the point onto the triangle plane is located.

> {{def}}{def:lec24:pt_dist}[3D Point-Triangle Distance] The distance between point $\mathbf{x}_p$ and triangle with vertices $\mathbf{x}_{t1}$, $\mathbf{x}_{t2}$, and $\mathbf{x}_{t3}$ can be defined as $$ \text{arg}\min_{\beta_1,\beta_2} \|\mathbf{x}_p - (\mathbf{x}_{t1} + \beta_1 (\mathbf{x}_{t2} - \mathbf{x}_{t1}) + \beta_2 (\mathbf{x}_{t3} - \mathbf{x}_{t1})) \| \quad \text{s.t.} \quad \beta_1,\beta_2 \geq 0, \ \beta_1+\beta_2 \leq 1 $$

> {{def}}{def:lec24:ee_dist}[3D Edge-Edge Distance] The distance between edge with end nodes $\mathbf{x}_{e1a}$ and $\mathbf{x}_{e1b}$ and edge with end nodes $\mathbf{x}_{e2a}$ and $\mathbf{x}_{e2b}$ can be defined as
> $$ \text{arg}\min_{\beta_1,\beta_2} \| \mathbf{x}_{e1a} + \beta_1 (\mathbf{x}_{e1b} - \mathbf{x}_{e1a}) - (\mathbf{x}_{e2a} + \beta_2 (\mathbf{x}_{e2b} - \mathbf{x}_{e2a})) \| \quad \text{s.t.} \quad \beta_1,\beta_2 \in [0,1] $$

> **{{rem}}{rem:lec24:smoothness}[Smoothness of 3D Distance Functions]** Note that the point-triangle distance is at least $C^1$ continuous everywhere. This means that even when the projected point is located on the borders of the piecewise function, the distance gradient still exists and is continuous. However, for edge-edge distance, when the edges are parallel, the distance function is only $C^0$ continuous, as the gradient of the expressions in adjacent regions do not agree. To address this issue, IPC {{#cite li2020incremental}} proposed multiplying a mollifier to the edge-edge barrier energy density function to make the potential $C^1$ continuous everywhere. This mollifier smoothly decreases to zero when the edges are parallel. This ensures that gradient-based optimization methods can still be applied efficiently to solve the problem.
