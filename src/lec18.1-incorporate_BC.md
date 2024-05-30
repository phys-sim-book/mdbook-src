## Incorporating Boundary Conditions

In the weak form we derived (see Equation {{eqref: eq:lec16:weakform_lagrangian}}), there is a boundary term $\int_{\partial\Omega^0} \mathbf{Q}_i(\mathbf{X}, t) \mathbf{T}_i(\mathbf{X}, t) \, ds(\mathbf{X})$ that describes the force acting on the boundary of the solid from the outside.

If there are no Dirichlet boundary conditions, the entire boundary is handled using **Neumann Boundary Conditions**, where the boundary force is specified as part of the problem setup. Recall that we discussed the [Dirichlet Boundary Condition](./lec5-dirichlet_BC_solve.md), where the displacements of the boundary are directly prescribed. In practice, external forces act on the Dirichlet boundaries to ensure their displacements precisely match the prescribed values, and these forces are calculated directly from those displacements.

In a solid simulation problem, boundaries can be either a Dirichlet boundary or a Neumann boundary, which can be described by a more general problem formulation in strong form:
$$
\begin{aligned}
R(\mathbf{X}, 0) \frac{\partial \mathbf{V}}{\partial t}(\mathbf{X}, t) = \nabla^\mathbf{X} \cdot \mathbf{P}(\mathbf{X}, t) + R(\mathbf{X}, 0) \mathbf{A}^\text{ext}(\mathbf{X}, t), & \\ \quad \forall \mathbf{X} \in \Omega^0 \text{ and } t \geq 0; & \\
\mathbf{x} = \mathbf{x}_D(\mathbf{X}, t), & \\ \quad \forall \mathbf{X} \in \Gamma_D \text{ and } t \geq 0; & \\
\mathbf{P}(\mathbf{X}, t) \mathbf{N}(\mathbf{X}) = \mathbf{T}_N(\mathbf{X}, t), & \\ \quad \forall \mathbf{X} \in \Gamma_N \text{ and } t \geq 0. &
\end{aligned}
{{numeq}}{eq:lec18:strong_form_with_BC}
$$

Here $\Gamma_N$ and $\Gamma_D$ are the Neumann and Dirichlet boundaries respectively, $\Gamma_N \cup \Gamma_D = \partial \Omega_0$, $\Gamma_N \cap \Gamma_D = \emptyset$, and $\mathbf{x}_D$ and $\mathbf{T}_N$ are given. After we derive the weak form of the momentum conservation (see Equation {{eqref: eq:lec18:strong_form_with_BC}}, first line), the boundary term $\int_{\partial\Omega^0} \mathbf{Q}_i(\mathbf{X}, t) \mathbf{T}_i(\mathbf{X}, t) \, ds(\mathbf{X})$ can be separately considered for Dirichlet and Neumann boundaries:
$$
\begin{aligned}
\int_{\partial\Omega^0} \mathbf{Q}_i(\mathbf{X}, t) \mathbf{T}_i(\mathbf{X}, t) \, ds(\mathbf{X}) \\
= \int_{\Gamma_D} \mathbf{Q}_i(\mathbf{X}, t) \mathbf{T}_{D|i}(\mathbf{X}, t) \, ds(\mathbf{X}) + \int_{\Gamma_N} \mathbf{Q}_i(\mathbf{X}, t) \mathbf{T}_{N|i}(\mathbf{X}, t) \, ds(\mathbf{X}).
\end{aligned}
$$


For Neumann boundaries, since the traction $\mathbf{T}_N(\mathbf{X}, t)$ is provided, the above integral can be directly evaluated after discretization. However, for Dirichlet boundaries, $\mathbf{T}_D(\mathbf{X}, t)$ remains unknown until we solve the problem. Therefore, a straightforward approach is to introduce the traction at Dirichlet boundaries as unknowns and solve the system that includes both the discretized weak form equations and the Dirichlet boundary conditions.



> **{{rem}}{rem:lec18:optimization_form}[Optimization Form]**
> In the [optimization form](./lec2-opt_framework.md), the potential energy does not include any Dirichlet boundaries, effectively ignoring the boundary integral in the weak form. This is valid because the Dirichlet boundary conditions will be enforced by the linear equality constraints, and the corresponding discretized weak form equation will be overwritten.
