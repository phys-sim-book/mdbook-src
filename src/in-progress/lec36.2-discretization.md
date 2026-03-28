# Discretization

The continuous model from {{eqref:eq:continuous:total_energy_matrix}} gives us a solid physical foundation. But to simulate it on a computer, how do we translate these continuous integrals and derivatives into a finite set of numbers? The answer is **discretization**.

We adopt a **staggered grid approach**, a technique successfully used in many rod simulators {{#cite kugelstadt2016position}}. The term "staggered" means we store different physical quantities at different locations. For a rod discretized into a chain of vertices and segments, we store positions $\mathbf{x}_i$ at the vertices and orientations $\mathbf{q}_i$ on the segments. This arrangement is numerically advantageous because it places the quantities exactly where they are needed to compute finite-difference approximations of the strains. A rod made of $N$ segments will be described by $N+1$ particles and $N-1$ quaternions.


Now, how do we approximate the strains? For stretching and shearing, we use a simple forward difference for the discrete tangent vector: $\partial_s \mathbf{r}(s) \approx (\mathbf{x}_{i+1} - \mathbf{x}_i) / l_i$, where $l_i$ is the rest length of the segment. This discretizes the continuous strain measure {{eqref:eq:continuous:stretch_strain}} to give the **discrete stretch/shear strain** $\mathbf{C}_i^{ss}$:
$$
{{numeq}}{eq:discrete:stretch_strain}
\mathbf{C}_i^{ss} = \frac{\mathbf{x}_{i+1} - \mathbf{x}_i}{l_i} - \mathbf{d}_{i,3}
$$
where $\mathbf{d}_{i,3}$ is the third director of the material frame defined by the segment's quaternion $\mathbf{q}_i$.

For bending and twisting, which occurs *at the vertices*, we need to measure the relative rotation between adjacent segments ($\mathbf{q}_{i-1}$ and $\mathbf{q}_i$). This discretizes the continuous Darboux vector {{eqref:eq:continuous:darboux_vector}} as:
$$
{{numeq}}{eq:discrete:darboux_vector}
\boldsymbol{\Omega}_i \approx \frac{2}{l} \mathfrak{I}(\overline{\mathbf{q}}_{i-1}\mathbf{q}_i)
$$
This has a direct geometric interpretation: it's the scaled imaginary part of the quaternion that rotates one frame to the next {{#cite kugelstadt2016position}}. 

The **discrete bend/twist strain** $\mathbf{C}_i^{bt}$ then penalizes the deviation of this relative rotation from its rest state. This can be expressed in terms of the Darboux vector as:
$$
{{numeq}}{eq:discrete:bend_twist_strain_omega}
\mathbf{C}_i^{bt} = \boldsymbol{\Omega}_i - \phi_i \boldsymbol{\Omega}_i^0
$$
where $\boldsymbol{\Omega}_i^0 = \frac{2}{l} \mathfrak{I}(\overline{\mathbf{q}}_{i-1}^0\mathbf{q}_i^0)$ is the Darboux vector at the rest state, and $\phi_i$ is a sign factor that ensures we always move towards the nearest pole:

$$\phi_i = \begin{cases} 
+1 & \text{if } \|\boldsymbol{\Omega}_i - \boldsymbol{\Omega}_i^0\|^2 \leq \|\boldsymbol{\Omega}_i + \boldsymbol{\Omega}_i^0\|^2 \\
-1 & \text{if } \|\boldsymbol{\Omega}_i - \boldsymbol{\Omega}_i^0\|^2 > \|\boldsymbol{\Omega}_i + \boldsymbol{\Omega}_i^0\|^2
\end{cases}$$

This $\phi_i$ factor is crucial because both $\boldsymbol{\Omega}_i^0$ and $-\boldsymbol{\Omega}_i^0$ correspond to the same physical rotation, and we want to choose the closest one for numerical stability.

Equivalently, the bend/twist strain can also be written directly in terms of quaternions:
$$
{{numeq}}{eq:discrete:bend_twist_strain}
\mathbf{C}_i^{bt} = \mathfrak{I}(\overline{\mathbf{q}}_{i-1}\mathbf{q}_i - \overline{\mathbf{q}}_{i-1}^0\mathbf{q}_i^0)
$$
where the superscript $0$ denotes the rest state.

It's worth noting a subtle point here: by only using the imaginary part $\mathfrak{I}(\cdot)$, this formulation creates an ambiguity, as different physical configurations can produce the same strain value, which can lead to instability. 

> **Why the Imaginary Part?** Taking only the imaginary part is important in the discrete scenario because the scalar part does not necessarily vanish as in the continuous case. In the continuous formulation, the scalar part of the quaternion derivative naturally vanishes, but in discrete finite differences, this is not guaranteed. Therefore, we extract only the imaginary (vector) part to maintain consistency with the continuous theory.
A recent improvement suggests using the full quaternion product for the Darboux vector to eliminate this ambiguity {{#cite hsu2023sag}}. For our purposes, we will proceed with the standard formulation, but this is an important detail for robust implementations.

Finally, with these discrete strain measures, we can write the total discrete potential energy as a sum over all elements. This discretizes the continuous energy {{eqref:eq:continuous:total_energy_matrix}} and can be written in either matrix form:
$$
{{numeq}}{eq:discrete:total_energy_matrix}
E^{total}(\mathbf{x}, \mathbf{q}) = \frac{1}{2}\sum_{i} l_i \cdot (\mathbf{C}_i^{ss})^T C^{\Gamma} \mathbf{C}_i^{ss} + \frac{1}{2}\sum_{j} l_j \cdot (\mathbf{C}_j^{bt})^T C^{\Omega} \mathbf{C}_j^{bt}
$$
or in the simpler scalar stiffness form (corresponding to {{eqref:eq:continuous:total_energy_scalar}}):
$$
{{numeq}}{eq:discrete:total_energy_scalar}
E^{total}(\mathbf{x}, \mathbf{q}) = \sum_i \frac{k_i^{ss}}{2} \|\mathbf{C}_i^{ss}\|^2 + \sum_j \frac{k_j^{bt}}{2} \|\mathbf{C}_j^{bt}\|^2
$$
where the discrete stiffnesses $k_i^*$ are derived from the continuous parameters and segment lengths.