## Simplified Models and Invertibility

> **{{def}}{def:lec13:corotated_linear_elasticity}[Corotated Linear Elasticity]**
> To make linear elasticity rotation-aware while maintaining its simplicity, we can introduce a base rotation $\mathbf{R}^n$ and construct an energy density function
$$
    \Psi_\text{LC}(\mathbf{F}) = \Psi_\text{lin}((\mathbf{R}^n)^T \mathbf{F}),
$$
> penalizing any deviation between $\mathbf{F}$ and this fixed $\mathbf{R}^n$. This is called corotated linear elasticity.

$\Psi_\text{LC}(\mathbf{F})$ remains a quadratic energy with respect to $\mathbf{F}$ and is very useful for dynamic simulations. At the beginning of the optimization for each time step $n+1$, we compute $\mathbf{R}^n$ as the closest rotation to $\mathbf{F}^n$:
$$
    \mathbf{R}^n = \text{arg}\min_{\mathbf{R}} \|\mathbf{F}^n - \mathbf{R} \|_\text{F}^2 \quad \text{s.t.} \quad \mathbf{R}^T = \mathbf{R}^{-1} \quad \text{and} \quad \det(\mathbf{R}) = 1.
    {{numeq}}{eq:lec13:min_rotation}
$$
As mentioned earlier, the solution is given by the Polar decomposition on $\mathbf{F}^n$, and with Polar SVD $\mathbf{F}^n=\mathbf{U}^n \mathbf{\Sigma}^n (\mathbf{V}^n)^T$, we have $\mathbf{R}^n = \mathbf{U}^n (\mathbf{V}^n)^T$. However, corotated linear elasticity is still not rotation invariant, as $\mathbf{R}^n$ does not change with $\mathbf{F}$ during the optimization. Thus, it is not suitable for large deformations.

For rotation invariant elastic models, practitioners in computer graphics have been simplifying them for visual computing purposes. For example, only keeping a $\mu$-term while ignoring the $\lambda$-term in the energy density function for more efficient computations:
$$
    \Psi_\text{R}(\mathbf{F}) = \frac{\mu}{4} \| \mathbf{F}^T \mathbf{F} - \mathbf{I} \|^2_\text{F}, \quad \text{or} \quad \Psi_\text{ARAP}(\mathbf{F}) = \mu \sum_i^d (\sigma_i - 1)^2, \quad \text{etc}.
    {{numeq}}{eq:lec13:simplified_models}
$$
Here $\Psi_\text{ARAP}(\mathbf{F})$ is called the **As-Rigid-As-Possible (ARAP)** energy, which is widely used in shape modeling, cloth simulation, and surface parameterization, etc. $\Psi_\text{R}(\mathbf{F})$, while being a higher-order polynomial of $\mathbf{F}$ compared to ARAP, can be computed without performing the expensive SVDs on $\mathbf{F}$.

For all the strain energy density functions we have looked at in this lecture, except Neo-Hookean, all others are defined on the whole domain $\mathbb{R}^{d\times d}$. Neo-Hookean energy density function is defined on $\{\mathbf{F} \ | \ \mathbf{F} \in \mathbb{R}^{d\times d}, \ \det(\mathbf{F})>0 \}$. Just like the barrier energy to prevent interpenetrations in IPC, $\Psi_\text{NH}(\mathbf{F})$ is also a barrier energy, which goes to infinity as $\det(\mathbf{F})$ approaches $0$, providing arbitrarily large elastic forces to prevent **inversion** ($\det(\mathbf{F}) \leq 0$).

Strain energy density functions allowing $\det(\mathbf{F}) \leq 0$ are also called **invertible elasticity models**. They are easy to deal with (no need for line search filtering), but do not guarantee non-inversion. Designing an invertible elastic energy that provides reasonably large resistance to inversion has drawn a lot of attention in computer graphics research {{#cite stomakhin2012energetically}} {{#cite smith2018stable}}.
