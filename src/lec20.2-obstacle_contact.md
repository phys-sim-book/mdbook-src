## Solid-Obstacle Contact

Recall that we used a conservative force model to approximate the contact traction $\mathbf{T}_C$, allowing it to be directly evaluated given the current configuration of the solids. This results in a contact potential:

$$
P_C = \int_{\Gamma_C} \frac{1}{2} b\left(\min_{\mathbf{X}_2 \in \Gamma_C - \mathcal{N}(\mathbf{X})} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) \, ds(\mathbf{X}),
$$

where $b()$ is the barrier energy density function, and $\mathcal{N}(\mathbf{X})$ is an infinitesimal region around $\mathbf{X}$ where contact is ignored for theoretical soundness.

>

For normal contact between simulated solids and collision obstacles (ignoring self-contact for now), $P_C$ can be written in a much simpler form
$$
\begin{aligned}
    P_C 
    & = \int_{\Gamma_S} \frac{1}{2} b\left(\min_{\mathbf{X}_2 \in \Gamma_O} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\ &+ \int_{\Gamma_O} \frac{1}{2} b\left(\min_{\mathbf{X}_2 \in \Gamma_S} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    & = \int_{\Gamma_S} b\left(\min_{\mathbf{X}_2 \in \Gamma_O} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    & = \int_{\Gamma_S} b\left(d^{PO}(\mathbf{x}(\mathbf{X}, t), O), \hat{d}\right) ds(\mathbf{X}).
\end{aligned}
$$
Here $\Gamma_S$ and $\Gamma_O$ are the boundaries of the simulated solids and obstacles respectively, $d^{PO}(\mathbf{x}(\mathbf{X},t), O) = \min_{\mathbf{X}_2 \in \Gamma_O} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|$ is the point-obstacle distance, and the simplification from two terms to one single term is due to symmetry in the continuous setting.
With triangle discretization, 
$$
    \int_{\Gamma_S} b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d}) ds(\mathbf{X}) \approx \sum_{e \in \mathcal{T}} \int_{\partial \Omega_e^0 \cap \Gamma_S} b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d}) ds(\mathbf{X}).
    {{numeq}}{eq:lec20:discrete_contact_potential}
$$
Similar to the derivation for Neumann boundaries, for any boundary node $\hat{a}$, with 2 incident triangles, let us look at one of the integral. Without loss of generality, we can assume $N_{\hat{a}} = \beta$ ($\mathbf{X}_{\hat{a}}$ corresponds to $\mathbf{X}_2$ in triangle $e$), and that $X_3$ is the other node of $e$ on the boundary edge. Then, switching the integration variables to $\beta$ gives us
$$
\begin{aligned}
   & \int_{\partial \Omega_e^0 \cap \Gamma_S} b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d}) ds(\mathbf{X}) \\
    = & \int_0^1 b (d^{PO}(\mathbf{x}(\beta\mathbf{X}_2 +(1-\beta) \mathbf{X}_3,t), O),\hat{d}) \left|\frac{\partial s}{\partial \beta}\right| d\beta.
    {{numeq}}{eq:lec20:point_obstacle_1}
\end{aligned}
$$
Since $b()$ and $d^{PO}()$ are both highly nonlinear functions, we could not obtain a closed-form expression for Equation {{eqref: eq:lec20:point_obstacle_1}}. If we take the two end points $\mathbf{X}_2$ and $\mathbf{X}_3$ as quadrature points both with weights $\frac{1}{2}$, we can approximate the integral as
$$
\begin{aligned}
    & \int_0^1 b (d^{PO}(\mathbf{x}(\beta\mathbf{X}_2 +(1-\beta) \mathbf{X}_3,t), O),\hat{d}) \left|\frac{\partial s}{\partial \beta}\right| d\beta \\
    \approx & \frac{1}{2} b (d^{PO}(\mathbf{x}(\mathbf{X}_2,t), O),\hat{d}) \left|\frac{\partial s}{\partial \beta}\right| + \frac{1}{2} b (d^{PO}(\mathbf{x}( \mathbf{X}_3,t), O),\hat{d}) \left|\frac{\partial s}{\partial \beta}\right|.
\end{aligned}
{{numeq}}{eq:lec20:ipc_end_point_quadrature}
$$
Then, the whole boundary integral can be approximated as
$$
    \int_{\Gamma_S} b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d}) ds(\mathbf{X}) \approx \sum_{\hat{a}}  \frac{\|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} - 1} \| + \|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} + 1} \|}{2} b (d^{PO}(\mathbf{x}_{\hat{a}}, O),\hat{d}),
$$
assuming that $\mathbf{X}_{\hat{a} - 1}$ and $\mathbf{X}_{\hat{a} + 1}$ are the two neighbors of $\mathbf{X}_{\hat{a}}$ on the boundary. This is now exactly what has been implemented in [Filter Line Search](./lec8-filter_line_search.md).

> **{{rem}}{rem:lec20:quadrature_choice}[Quadrature Choice for Line Segment]** Selecting the two end points ($\beta = 0, 1$) as quadrature points for a line segment integral (Equation {{eqref: eq:lec20:ipc_end_point_quadrature}}) is not a common design choice. Typically, Gaussian quadrature would use $\beta = \frac{3 \pm \sqrt{3}}{6}$. The advantage of choosing $\beta = 0, 1$ is that it results in fewer quadrature points globally, thus reducing computational costs, as neighboring edges share end points.


To see how $P_C$ connects to the boundary integral (Equation {{eqref: eq:lec20:bi_disc_weak}}) in the discrete weak form, let us take the derivative of the discretized contact potential (Equation {{eqref: eq:lec20:discrete_contact_potential}}) with respect to $\mathbf{x}_{\hat{a}}$:
$$
\begin{aligned}
   & -\frac{\partial (\sum_{e \in \mathcal{T}} \int_{\partial \Omega_e^0 \cap \Gamma_S} b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d}) ds(\mathbf{X}))}{\partial \mathbf{x}_{\hat{a}}} \\
   = &
   \sum_{e \in \mathcal{T}} \int_{\partial \Omega_e^0 \cap \Gamma_S} -\frac{\partial b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d})}{\partial \mathbf{x}} \frac{\partial \mathbf{x}}{\partial \mathbf{x}_{\hat{a}}} ds(\mathbf{X}) \\
   = & \sum_{e \in \mathcal{T}} \int_{\partial \Omega_e^0 \cap \Gamma_S} -\frac{\partial b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d})}{\partial \mathbf{x}} N_{\hat{a}}(\mathbf{X}) ds(\mathbf{X}).
\end{aligned}
$$
Then we also verified that $\mathbf{T}_C(\mathbf{X}, t) = -\frac{\partial b (d^{PO}(\mathbf{x}(\mathbf{X},t), O),\hat{d})}{\partial \mathbf{x}}$ here.
