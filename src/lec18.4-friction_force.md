## Friction Force

Analogous to [Frictional Contact](./lec9-friction.md), maximizing the dissipation rate subject to the Coulomb constraint defines friction forces per unit area variationally:

$$
\begin{aligned}
    \mathbf{T}_F(\mathbf{X}, t) = \argmin_{\boldsymbol{\beta} \in \mathbb{R}^d} \boldsymbol{\beta}^T \mathbf{V}_F(\mathbf{X}, t) \\
    \text{s.t.} \quad \|\boldsymbol{\beta}\| \leq \mu \|\mathbf{T}_C(\mathbf{X}, t)\| \ \ \text{and} \ \ \boldsymbol{\beta} \cdot \mathbf{N}(\mathbf{X}, t) = 0.
\end{aligned}
{{numeq}}{eq:lec18:frictionForceDef}
$$

Here, $\mathbf{V}_F(\mathbf{X}, t) = \mathbf{V}(\mathbf{X}, t) - \mathbf{V}(\mathbf{X}_2, t)$ is the relative sliding velocity between $\mathbf{X}$ and the closest point $\mathbf{X}_2 = \text{arg}\min_{\mathbf{X}_2 \in \Gamma_C - \mathcal{N}(\mathbf{X)}} \|\mathbf{X} - \mathbf{X}_2\|$, $\mu$ is the coefficient of friction, $\mathbf{T}_C$ is the normal contact force per unit area, and $\mathbf{N}$ is the normal direction.

This is equivalent to:

$$
\mathbf{T}_F(\mathbf{X}, t) = - \mu \|\mathbf{T}_C(\mathbf{X}, t)\| \ f(\|\mathbf{V}_F(\mathbf{X}, t)\|) \ \mathbf{s}(\mathbf{V}_F(\mathbf{X}, t)),
{{numeq}}{eq:lec18:smooth-frict}
$$

with $\mathbf{s}(\mathbf{V}_F) = \frac{\mathbf{V}_F}{\|\mathbf{V}_F\|}$ when $\|\mathbf{V}_F\| > 0$, while $\mathbf{s}(\mathbf{V}_F)$ takes any unit vector orthogonal to $\mathbf{N}(\mathbf{X}, t)$ when $\|\mathbf{V}_F\| = 0$. 

In addition, the friction scaling function $f$ is also nonsmooth with respect to $\mathbf{V}_F$, since $f(\|\mathbf{V}_F\|) = 1$ when $\|\mathbf{V}_F\| > 0$, and $f(\|\mathbf{V}_F\|) \in [0,1]$ when $\|\mathbf{V}_F\| = 0$. These nonsmooth properties can severely hinder or even break the convergence of gradient-based optimization. The mollification of the friction-velocity relationship here follows the same approach as in [Frictional Contact](./lec9-friction.md).
