## Rigid Null Space and Rotation Invariance

As mentioned in the previous lecture, for a solid undergoing only translational and/or rotational motions, no elastic potential energy is stored, and thus no elasticity force is exerted. This implies that any strain energy density functions $\Psi(\mathbf{F})$ have a **rigid null space**, meaning that $\Psi(\mathbf{F})$ should remain $0$ if the input deformation gradient is any rotation matrix $\mathbf{R}$:
$$
    \Psi(\mathbf{F}) = 0 \quad \forall \ \mathbf{F} = \mathbf{R}.
$$
A square matrix $\mathbf{F}$ is a rotation matrix if and only if:
$$
    \mathbf{F}^T = \mathbf{F}^{-1} \quad \text{and} \quad J \equiv \text{det}(\mathbf{F}) = 1.
$$
From this definition, a straightforward formulation for $\Psi(\mathbf{F})$ emerges, penalizing any deviation of $\mathbf{F}$ from being a rotation matrix with quadratic terms:
$$
    \Psi(\mathbf{F}) = \frac{\mu}{4} \| \mathbf{F}^T \mathbf{F} - \mathbf{I} \|^2_F + \frac{\lambda}{2} (J - 1)^2.
    {{numeq}}{eq:lec13:strain_density_intuitive}
$$
Here, $\mu$ and $\lambda$ are the stiffness parameters, with the first term derived from right-multiplying $\mathbf{F}$ to both sides of $\mathbf{F}^T = \mathbf{F}^{-1}$. This intuitive formulation closely aligns with how many standard strain energy density functions are constructed.

> **{{def}}{def:lec13:neo_hookean}[Neo-Hookean Elasticity]**
> The Neo-Hookean elasticity model is characterized by the following energy density function:
$$
    \Psi_\text{NH}(\mathbf{F}) = \frac{\mu}{2}\left(\text{tr}(\mathbf{F}^T\mathbf{F})-d\right)-\mu\ln(J)+\frac{\lambda}{2}\ln^2(J).
    {{numeq}}{eq:lec13:strain_density_NH}
$$
> Taking the derivative of $\Psi_\text{NH}(\mathbf{F})$ with respect to $\mathbf{F}$, we obtain:
$$
    \frac{\partial \Psi}{\partial \mathbf{F}}(\mathbf{F}) = \mu(\mathbf{F}-\mathbf{F}^{-T})+\lambda\ln(J)\mathbf{F}^{-T}.
$$
> From this gradient, it is evident that the $\mu$-term achieves a local minimum when $\mathbf{F} - \mathbf{F}^{-T} = 0$ (i.e., $\mathbf{F}^T = \mathbf{F}^{-1}$), and for the $\lambda$-term, the local minimum occurs at $J=1$.

> **{{def}}{def:lec13:lame_parameters}[Lame Parameters]**
> In standard strain energy density functions, the stiffness parameters $\mu$ and $\lambda$ are known as Lame parameters. These parameters are directly related to the **Young's modulus** $E$, which measures resistance to stretching, and the **Poisson's ratio** $\nu$, which measures the incompressibility of the solid:
$$
    \mu = \frac{E}{2(1+\nu)}, \quad \lambda = \frac{E\nu}{(1+\nu)(1-2\nu)}.
$$

> **{{def}}{def:lec13:rotation_invariance}[Rotation Invariance]**
> The energy density function for any nonlinear elastic model is rotation invariant. Mathematically, this is expressed as:
$$
    \Psi(\mathbf{F}) = \Psi(\mathbf{R} \mathbf{F}) \quad \forall \ \mathbf{F} \in \mathbb{R}^{d\times d} \ \text{and} \ d \times d \ \text{rotation matrix} \ \mathbf{R}.
    {{numeq}}{eq:lec13:rot_invariant}
$$
> Intuitively, this means that any rotations applied after deformation should not alter the value of the strain energy density function.

However, the simplest strain energy density function, **linear elasticity**, does not include rigid modes in its null space nor does it satisfy Equation {{eqref: eq:lec13:rot_invariant}}. This is because linear elasticity is specifically designed for **infinitesimal strains**, where no significant rotations are involved.

> **{{def}}{def:lec13:linear_elasticity}[Linear Elasticity]**
> Linear elasticity has the energy density function
$$
    \Psi_\text{lin}(\mathbf{F}) = \mu \|\bm{\epsilon}\|^2_F + \frac{\lambda}{2} \text{tr}^2(\bm{\epsilon}).
    {{numeq}}{eq:lec13:strain_energy_lin}
$$
> Here $\bm{\epsilon} = \frac{1}{2}(\mathbf{F} + \mathbf{F}^T) - \mathbf{I}$ is the **small strain tensor**, and we see that $\Psi_\text{lin}(\mathbf{F})$ is a quadratic function of $\mathbf{F}$.

Notably, the linear elasticity model with the corresponding Lame parameters is calibrated to real-world experiments under conditions of small deformations. In such circumstances, all standard strain energy density functions must align with linear elasticity. The consistency between these models and linear elasticity will be concisely demonstrated after we introduce the polar singular value decomposition of $\mathbf{F}$ in the next section.

Rotation invariance (Equation {{eqref: eq:lec13:rot_invariant}}) should not be confused with the isotropic property of certain elastic models.

> **{{def}}{def:lec13:isotropic_elasticity}[Isotropic Elasticity]**
> The energy density function of isotropic elastic models satisfies
$$
    \Psi(\mathbf{F}) = \Psi(\mathbf{F} \mathbf{R}) \quad \forall \ \mathbf{F} \in \mathbb{R}^{d\times d} \ \text{and} \ d \times d \ \text{rotation matrix} \ \mathbf{R}.
    {{numeq}}{eq:lec13:isotropic_condition}
$$
> This implies that the same amount of stretch in any direction results in the same energy change. Consequently, there are no special directions in which the material is harder or easier to deform than others.

Neo-Hookean (Equation {{eqref: eq:lec13:strain_density_NH}}) and our intuitive model (Equation {{eqref: eq:lec13:strain_density_intuitive}}) are both examples of isotropic models. However, linear elasticity (Equation {{eqref: eq:lec13:strain_energy_lin}}) does not meet this condition (Equation {{eqref: eq:lec13:isotropic_condition}}), as it is not designed to handle rotational motions effectively.

For **anisotropic elastic models**, the resistance to stretch varies depending on the direction. Materials such as cloth, bones, muscles and wood are examples of anisotropic materials, exhibiting different mechanical properties in different directions.
