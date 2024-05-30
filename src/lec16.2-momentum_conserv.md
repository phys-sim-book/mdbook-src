## Conservation of Momentum

In the continuous setting, forces are categorized into body forces (also known as external forces, such as gravity) and surface forces (or internal forces, typically stress-based, like those arising from elasticity). We define stress-based forces through a traction field, whose existence is assumed. The traction, or force per unit area, is represented by the field $\mathbf{T}(\cdot, \mathbf{N}, t): \Omega^0 \rightarrow \mathbb{R}^d$ and is defined by the equation:
$$
\text{force}_S(B^0_\epsilon) = \int_{\partial B^0_\epsilon} \mathbf{T}(\mathbf{X}, \mathbf{N}(\mathbf{X})) \, ds(\mathbf{X}),
$$
where $\mathbf{N}$ represents the outward-pointing normal direction in the material space. Here, $\text{force}_S(B^0_\epsilon)$ denotes the net force exerted from the material outside $\partial B^0_\epsilon$ on the material inside $B^0_\epsilon$ through their interface. The function $\mathbf{T}(\mathbf{X}, \mathbf{N}, t)$ quantifies the force per unit area ($d=3$) or length ($d=2$) that material on the $\mathbf{N}^+$ side exerts at point $\mathbf{X}$ on material on the $\mathbf{N}^-$ side.

It can be shown that this implies the existence of a stress field (first Piola-Kirchoff stress) $\mathbf{P}(\cdot, t): \Omega^0 \rightarrow \mathbb{R}^{d \times d}$ with:
$$
\mathbf{T}(\mathbf{X}, \mathbf{N}, t) = \mathbf{P}(\mathbf{X}, t) \mathbf{N}.
$$

Then, by applying Newton's second law on $B^0_\epsilon$, we can express the conservation of momentum as:
$$
\begin{aligned}
& \int_{B^0_\epsilon} R(\mathbf{X}, 0) \frac{\partial \mathbf{V}}{\partial t}(\mathbf{X}, t) \, d\mathbf{X} \\
= & \int_{\partial B^0_\epsilon} \mathbf{P}(\mathbf{X}, t) \mathbf{N}(\mathbf{X}) \, ds(\mathbf{X}) + \int_{B^0_\epsilon} R(\mathbf{X}, 0) \mathbf{A}^\text{ext}(\mathbf{X}, t) \, d\mathbf{X},
\end{aligned}
{{numeq}}{eq:lec16:conserve_momentum_pBeps}
$$
for all $B^0_\epsilon \subset \Omega^0$ and $t \geq 0$.

Applying the divergence theorem, we can transform the boundary integral in Equation {{eqref: eq:lec16:conserve_momentum_pBeps}} into a volume integral and obtain:
$$
\begin{aligned}
& \int_{B^0_\epsilon} R(\mathbf{X}, 0) \frac{\partial \mathbf{V}}{\partial t}(\mathbf{X}, t) \, d\mathbf{X} \\
= & \int_{B^0_\epsilon} \nabla^\mathbf{X} \cdot \mathbf{P}(\mathbf{X}, t) \, d\mathbf{X} + \int_{B^0_\epsilon} R(\mathbf{X}, 0) \mathbf{A}^\text{ext}(\mathbf{X}, t) \, d\mathbf{X},
\end{aligned}
{{numeq}}{eq:lec16:conserve_momentum_Beps}
$$
for all $B^0_\epsilon \subset \Omega^0$ and $t \geq 0$.

> **{{def}}{def:lec16:div_theorem}[Divergence Theorem for Vectors]**
> For a vector-valued function $\mathbf{f}(\mathbf{x}) : \Omega \rightarrow \mathbb{R}^d$ defined on a closed domain $\Omega$, let $\mathbf{n}(\mathbf{x})$ be the outward-pointing normal on the boundary of this domain, the following equality holds:
$$
\int_{\partial \Omega} \mathbf{f} \cdot \mathbf{n} \, ds(\mathbf{x}) = \int_{\Omega} \nabla \cdot \mathbf{f} \, d\mathbf{x}.
$$
This theorem allows us to conveniently transform between boundary and volume integrals.

Here the divergence operator $\nabla \cdot$ acts on every row vector of $\mathbf{P}$ independently and results in a column vector: $(\nabla^\mathbf{X} \cdot \mathbf{P})_i = \sum_j P_{ij,j}$. Since Equation {{eqref: eq:lec16:conserve_momentum_Beps}} also holds for arbitrary $B^0_\epsilon$, we arrive at the strong form of the force balance equation by removing the integration:
$$
\begin{aligned}
R(\mathbf{X}, 0) \frac{\partial \mathbf{V}}{\partial t}(\mathbf{X}, t) = \nabla^\mathbf{X} \cdot \mathbf{P}(\mathbf{X}, t) + R(\mathbf{X}, 0) \mathbf{A}^\text{ext}(\mathbf{X}, t), \\ \forall \ \mathbf{X} \in \Omega^0 \ \text{and} \ t \geq 0.
\end{aligned}
{{numeq}}{eq:lec16:momentum_conserve_derived}
$$

> **{{rem}}{rem:lec16:conserv_momentum}[Momentum Conservation in Solid Simulation]**
> Conservation of momentum is the primary governing equation we use to simulate solids. As discussed previously, both the acceleration, denoted by $\frac{\partial \mathbf{V}}{\partial t}(\mathbf{X}, t)$, and the internal force, expressed as $\nabla^\mathbf{X} \cdot \mathbf{P}(\mathbf{X}, t)$, can be described using world space coordinates $\mathbf{x}$. With all other relevant quantities established, we incrementally solve for $\mathbf{x}$ to get dynamic motions step by step.

