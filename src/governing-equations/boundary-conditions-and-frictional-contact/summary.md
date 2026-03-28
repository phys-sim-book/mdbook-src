## Summary

We have discussed Neumann and Dirichlet boundary conditions as well as frictional contact in the continuous setting to complete a rigorous problem formulation. Combining everything in strong form, for all $t \geq 0$:

$$
\begin{aligned}
    R(\mathbf{X}, 0) \frac{\partial \mathbf{V}}{\partial t}(\mathbf{X}, t) = \nabla^\mathbf{X} \cdot \mathbf{P}(\mathbf{X}, t) + R(\mathbf{X}, 0) \mathbf{A}^\text{ext}(\mathbf{X}, t), \quad &\forall \ \mathbf{X} \in \Omega^0; \\
    \mathbf{x} = \mathbf{x}_D(\mathbf{X}, t), \quad &\forall \ \mathbf{X} \in \Gamma_D; \\
    \mathbf{P}(\mathbf{X}, t) \mathbf{N}(\mathbf{X}) = \mathbf{T}_N(\mathbf{X}, t) + \mathbf{T}_C(\mathbf{X}, t) + \mathbf{T}_F(\mathbf{X}, t), \quad &\forall \ \mathbf{X} \in \Gamma_N; \\
    \phi(\mathbf{X}, t) : \Omega^0 \rightarrow \Omega^t \text{ is bijective,} \quad &\forall \ \mathbf{X} \in \Omega^0; \\
    \mathbf{T}_F(\mathbf{X}, t) = \argmin_{\boldsymbol{\beta} \in \mathbb{R}^d} \boldsymbol{\beta}^T \mathbf{V}_F(\mathbf{X}, t) \\
    \text{s.t.} \quad \|\boldsymbol{\beta}\| \leq \mu \|\mathbf{T}_C(\mathbf{X}, t)\| \ \ \text{and} \ \ \boldsymbol{\beta} \cdot \mathbf{N}(\mathbf{X}, t) = 0, \quad &\forall \ \mathbf{X} \in \Gamma_C.
\end{aligned}
{{numeq}}{eq:lec18:strong_form_with_BC_and_fric_contact}
$$

After deriving the weak form of the momentum equation, the boundary integral term can be separated as follows:

$$
\begin{aligned}
    \int_{\partial \Omega^0} Q_i(\mathbf{X}, t) \mathbf{T}_i(\mathbf{X}, t) \, ds(\mathbf{X}) = & \int_{\Gamma_D} Q_i(\mathbf{X}, t) \mathbf{T}_{D|i}(\mathbf{X}, t) \, ds(\mathbf{X}) \\
    & + \int_{\Gamma_N} Q_i(\mathbf{X}, t) \mathbf{T}_{N|i}(\mathbf{X}, t) \, ds(\mathbf{X}) \\
    & + \int_{\Gamma_C} Q_i(\mathbf{X}, t) \mathbf{T}_{C|i}(\mathbf{X}, t) \, ds(\mathbf{X}) \\
    & + \int_{\Gamma_C} Q_i(\mathbf{X}, t) \mathbf{T}_{F|i}(\mathbf{X}, t) \, ds(\mathbf{X}).
\end{aligned}
{{numeq}}{eq:lec18:boundary_int_with_fric_contact}
$$

Here, only the Neumann force $\mathbf{T}_N(\mathbf{X}, t)$ is given, while all other boundary forces can be determined after solving the coupled system. Fortunately, Dirichlet boundary conditions can be enforced straightforwardly in the optimization framework as linear equality constraints. Frictional contact forces $\mathbf{T}_C(\mathbf{X}, t)$ and $\mathbf{T}_F(\mathbf{X}, t)$ can both be smoothly approximated as conservative forces with controllable error.

In the next chapter, we will discuss discretizing the weak form using the finite element method (FEM), connecting the derivations in this chapter to the discrete simulation methods.
