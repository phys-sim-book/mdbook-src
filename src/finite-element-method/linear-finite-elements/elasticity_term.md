## Elasticity Term

For the elasticity term $\int_{\Omega^0} N_{\hat{a},j}(\mathbf{X}) P_{\hat{i}j}(\mathbf{X}, t^n) d\mathbf{X}$ in the discrete weak form system in Equation {{eqref: eq:lec19:discrete_weak_form_BE}}, we can write it as the summation of integrals on each triangle $e$ in vector form:

$$
\begin{aligned}
& \int_{\Omega^0} N_{\hat{a},j}(\mathbf{X}) P_{\hat{i}j}(\mathbf{X}, t^n) d\mathbf{X} = \int_{\Omega^0} (\mathbf{P}(\mathbf{X}, t^n) \nabla^{\mathbf{X}} N_{\hat{a}}(\mathbf{X}))_{\hat{i}} \ d\mathbf{X} \\
& = \sum_{e \in \mathcal{T}} \int_{\Omega^0_e} (\mathbf{P}(\mathbf{X}, t^n) \nabla^{\mathbf{X}} N_{\hat{a}}(\mathbf{X}))_{\hat{i}} \ d\mathbf{X}.
\end{aligned}
{{numeq}}{eq:lec19:elastic_integral}
$$

Analogously, this summation also only needs to involve the incident triangles of node $\hat{a}$.

Recall from [Strain Energy](./lec13-strain_energy.md), to compute the first Piola-Kirchoff stress $\mathbf{P}(\mathbf{X}, t^n)$, we only need the deformation gradient $\mathbf{F}(\mathbf{X}, t^n)$. From Section [Kinematics](./lec12-kinematics.md), we know that $\mathbf{F} = \frac{\partial \mathbf{x}}{\partial \mathbf{X}}$. Applying the chain rule with the parameter space variables $(\beta, \gamma)$ as intermediates, we have:

$$
\begin{aligned}
&\mathbf{F} = \frac{\partial \mathbf{x}}{\partial (\beta, \gamma)} \left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma)}\right)^{-1} 
\approx \frac{\partial \hat{\mathbf{x}}}{\partial (\beta, \gamma)} \left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma)}\right)^{-1} \\
& = [\mathbf{x}_2 - \mathbf{x}_1, \mathbf{x}_3 - \mathbf{x}_1] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1]^{-1},
\end{aligned}
{{numeq}}{eq:lec19:compute_F}
$$

which is exactly the same as Equation {{eqref: eq:lec15:compute_F}} from our earlier implementation (Section [Inversion-Free Elasticity](./lec15-inv_free_elasticity.md)). Here, we also see that with linear finite elements, the deformation gradient field is piecewise constant in $\Omega^0$, so is $\mathbf{P}$.

Then for $\nabla^{\mathbf{X}} N_{\hat{a}}(\mathbf{X})$, depending on the index of $\hat{a}$ in triangle $e$, we can derive it again using parameter space variables as:

$$
\begin{aligned}
    \nabla^{\mathbf{X}} N_1(\mathbf{X}) & = \frac{\partial (1 - \beta - \gamma)}{\partial \mathbf{X}} = \left(\frac{\partial (1 - \beta - \gamma)}{\partial (\beta, \gamma)} (\frac{\partial \mathbf{X}}{\partial (\beta, \gamma)})^{-1}\right)^T \\ 
    &= ([-1, -1] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1]^{-1})^T \\
    \nabla^{\mathbf{X}} N_2(\mathbf{X}) & = \frac{\partial \beta}{\partial \mathbf{X}} = \left(\frac{\partial \beta}{\partial (\beta, \gamma)} \left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma)}\right)^{-1}\right)^T \\
    = ([1, 0] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1]^{-1})^T \\
    \nabla^{\mathbf{X}} N_3(\mathbf{X}) & = \frac{\partial \gamma}{\partial \mathbf{X}} = \left(\frac{\partial \gamma}{\partial (\beta, \gamma)} \left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma)}\right)^{-1}\right)^T \\
    = ([0, 1] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1]^{-1})^T.
\end{aligned}
$$

This also allows us to see that $\mathbf{P}(\mathbf{X}, t^n) \nabla^{\mathbf{X}} N_{\hat{a}}(\mathbf{X})$ is constant within any triangle $e$ and it is equivalent to $\frac{\partial \Psi_e}{\partial \mathbf{x}_{\hat{a}}}$ since:

$$
\begin{aligned}
\frac{\partial \Psi_e}{\partial \mathbf{x}_{\hat{a}}} 
= \frac{\partial \Psi_e}{\partial \mathbf{F}} \frac{\partial \mathbf{F}}{\partial \mathbf{x}_{\hat{a}}}
= \mathbf{P} \nabla^{\mathbf{X}} N_{\hat{a}}.
\end{aligned}
$$

Substituting $\frac{\partial \Psi_e}{\partial \mathbf{x}_{\hat{a}}}$ into Equation {{eqref: eq:lec19:elastic_integral}} we obtain:

$$
\begin{aligned}
\int_{\Omega^0} N_{\hat{a},j}(\mathbf{X}) P_{\hat{i}j}(\mathbf{X}, t^n) d\mathbf{X} 
& = \sum_{e \in \mathcal{T}} \int_{\Omega^0_e} (\mathbf{P}(\mathbf{X}, t^n) \nabla^{\mathbf{X}} N_{\hat{a}}(\mathbf{X}))_{\hat{i}} \ d\mathbf{X} \\
& = \sum_{e \in \mathcal{T}} \int_{\Omega^0_e} \left(\frac{\partial \Psi_e}{\partial \mathbf{x}_{\hat{a}}}\right)_{\hat{i}} \ d\mathbf{X} \\
& = \sum_{e \in \mathcal{T}} A_e \left(\frac{\partial \Psi_e}{\partial \mathbf{x}_{\hat{a}}}\right)_{\hat{i}},
\end{aligned}
$$

which is exactly how nodal elasticity force is computed in Section [Inversion-Free Elasticity](./lec15-inv_free_elasticity.md). This also indicates that the total elasticity potential can be calculated as $\sum_{e \in \mathcal{T}} A_e \Psi_e$, which is $\int_{\Omega^0} \Psi(\mathbf{X}) d\mathbf{X}$ before spatial discretization.

> **{{rem}}{rem:lec19:linear_FEM_elasticity} [Linear FEM]**
> Linear FEM refers to $\mathbf{x}$ being a piecewise linear function of $\mathbf{X}$, but the elasticity model can still be nonlinear, i.e. $\mathbf{P}$ can be a nonlinear function of $\mathbf{F}$.
