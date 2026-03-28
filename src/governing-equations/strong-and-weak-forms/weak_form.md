## Weak Form

First, since the external force term $R(\mathbf{X},0) \mathbf{A}^{\text{ext}}(\mathbf{X}, t)$ resembles a lot to the time derivative of the momentum on the left-hand side, we will ignore it during the derivation for simplicity. 
Then, for an arbitrary test function $\mathbf{Q}(\cdot,t): \Omega^0 \rightarrow \mathbb{R}^d$, let's compute the dot product to both sides of Equation {{eqref: eq:lec16:momentum_conserve_derived}} and integrate over $\Omega^0$ to generate the weak form:
$$
\begin{aligned}
& \int_{\Omega^0} R(\mathbf{X},0) \mathbf{Q}(\mathbf{X},t) \cdot \mathbf{A}(\mathbf{X},t) d\mathbf{X} \\
= & \int_{\Omega^0} \mathbf{Q}(\mathbf{X},t) \cdot (\nabla^\mathbf{X} \cdot \mathbf{P}(\mathbf{X},t)) d\mathbf{X}, 
\forall \ \mathbf{Q}(\cdot,t) : \Omega^0 \rightarrow \mathbb{R}^d \ \text{and} \ t\geq 0.
\end{aligned}
{{numeq}}{eq:lec16:weak_form}
$$
Here we denote $\mathbf{A}(\mathbf{X}, t) = \frac{\partial \mathbf{V}}{\partial t}(\mathbf{X}, t)$. Without going into details on finite element analysis, we claim that the weak form is sufficiently equivalent to the strong form since Equation {{eqref: eq:lec16:weak_form}} is required to hold for arbitrary $\mathbf{Q}(\cdot, t)$, and solving the weak form provides us a solution that is a "good enough" soution to the original problem.

With index notation where $A_i$ means the $i$-th component of vector-valued function $\mathbf{A} : \Omega^0 \rightarrow \mathbb{R}^d$, and $A_{i,j}$ means $\frac{\partial A_i}{\partial X_j}$, we can rewrite Equation {{eqref: eq:lec16:weak_form}} as
$$
\int_{\Omega^0} R(\mathbf{X},0) \sum_i Q_i(\mathbf{X},t) A_i(\mathbf{X},t) d\mathbf{X} = \int_{\Omega^0} \sum_i Q_i(\mathbf{X},t) \sum_j P_{ij,j}(\mathbf{X},t) d\mathbf{X}.
{{numeq}}{eq:lec16:weak_form_index}
$$
If we further omit the summation symbol and let the repetitive subscripts represent summation (this is known as Einstein notation), we obtain
$$
\int_{\Omega^0} R(\mathbf{X},0) Q_i(\mathbf{X},t) A_i(\mathbf{X},t) d\mathbf{X} = \int_{\Omega^0} Q_i(\mathbf{X},t) P_{ij,j}(\mathbf{X},t) d\mathbf{X}.
{{numeq}}{eq:lec16:weak_form_index_nosum}
$$
Now applying Integration By Parts on the right-hand side, we can rewrite Equation {{eqref: eq:lec16:weak_form_index_nosum}} as
$$
\begin{aligned}
&\int_{\Omega^0} R(\mathbf{X},0) Q_i(\mathbf{X},t) A_i(\mathbf{X},t) d\mathbf{X} \\
&= \int_{\Omega^0} (\nabla \cdot (Q_i(\mathbf{X},t) \mathbf{P}_i(\mathbf{X},t)) - \nabla Q_i(\mathbf{X},t) \cdot \mathbf{P}_i(\mathbf{X},t)) d\mathbf{X} \\
&= \int_{\Omega^0} ((Q_i(\mathbf{X},t) P_{ij}(\mathbf{X},t))_{,j} - Q_{i,j}(\mathbf{X},t) P_{ij}(\mathbf{X},t)) d\mathbf{X}.
{{numeq}}{eq:lec16:weak_form_index_IBP}
\end{aligned}
$$

> **{{def}}{def:lec16:integration_by_parts}[Integration By Parts]**
> For a scalar-valued function $u(\mathbf{x})$ and a vector-valued function (vector field) $\mathbf{V}(\mathbf{x})$, the product rule for divergence states that:
$$
\nabla \cdot (u(\mathbf{x}) \mathbf{V}(\mathbf{x})) = u(\mathbf{x}) \nabla \cdot \mathbf{V}(\mathbf{x}) + \nabla u(\mathbf{x}) \cdot \mathbf{V}(\mathbf{x}).
$$
> Integrating both sides on domain $\Omega$ then gives:
$$
\int_\Omega \nabla \cdot (u(\mathbf{x}) \mathbf{V}(\mathbf{x})) d\mathbf{x} = \int_\Omega u(\mathbf{x}) \nabla \cdot \mathbf{V}(\mathbf{x}) d\mathbf{x} + \int_\Omega \nabla u(\mathbf{x}) \cdot \mathbf{V}(\mathbf{x}) d\mathbf{x}.
$$

Then if we further apply the divergence theorem on the first part of the right-hand side of Equation {{eqref: eq:lec16:weak_form_index_IBP}}, we obtain
$$
\begin{aligned}
& \int_{\Omega^0} R(\mathbf{X},0) Q_i(\mathbf{X},t) A_i(\mathbf{X},t) d\mathbf{X} \\
&= \int_{\partial\Omega^0} Q_i(\mathbf{X},t) P_{ij}(\mathbf{X},t) N_j(\mathbf{X}) ds(\mathbf{X}) - \int_{\Omega^0} Q_{i,j}(\mathbf{X},t) P_{ij}(\mathbf{X},t) d\mathbf{X}.
{{numeq}}{eq:lec16:weak_form_index_final}
\end{aligned}
$$
The quantity $P_{ij} N_j$ would be specified as a boundary condition. If we let $\mathbf{T}(\mathbf{X},t)$ be the boundary force per unit reference area (traction) with $T_i = P_{ij} N_j$, then we can say that the conservation of momentum implies that $\forall \mathbf{Q}(\cdot,t): \Omega^0 \rightarrow \mathbb{R}^d$
$$
\begin{aligned}
&\int_{\Omega^0} R(\mathbf{X},0) Q_i(\mathbf{X},t) A_i(\mathbf{X},t) d\mathbf{X} \\
&=\int_{\partial\Omega^0} Q_i(\mathbf{X},t) T_i(\mathbf{X},t) ds(\mathbf{X}) - \int_{\Omega^0} Q_{i,j}(\mathbf{X},t) P_{ij}(\mathbf{X},t) d\mathbf{X}.
{{numeq}}{eq:lec16:weakform_lagrangian}
\end{aligned}
$$
This is momentum conservation's weak form written in $\Omega^0$.

<!-- <p style="color:red">TODO: discuss the pros and cons comparing weak form to strong form</p> -->

> **{{rem}}{rem:lec16:why_weak_form}[Why Weak Form]**
In finite element method (FEM) for solids, conservation of momentum is formulated in the weak form rather than directly discretizing the strong form due to specific advantages. The strong form requires the displacement field and its derivatives to be continuously differentiable across the entire domain, which is difficult to achieve in practical scenarios involving complex geometries or material discontinuities. On the other hand, the weak form only requires the displacement field itself to be continuous, relaxing the need for continuous derivatives. This makes the weak form more adaptable to irregular mesh geometries and better suited for incorporating boundary conditions and handling interface problems. The weak form's integration-based approach reduces the sensitivity to local irregularities, making it more stable and robust for numerical computation in solid mechanics. Thus, while the strong form provides a direct representation of physical laws, its direct discretization is less practical for the computational demands and complexities typical in FEM analyses.