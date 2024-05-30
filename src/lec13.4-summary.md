## Summary

The **elastic potential energy** $P_e$ is an integration of the **strain energy density function** $\Psi(\mathbf{F})$ at every local point in the solid domain. From the **rigid null space**, we derived an intuitive formulation of the strain energy density function, similar in structure to standard models like **Neo-Hookean**. Nonlinear elastic models are also **rotation invariant**, meaning any rotations applied after the deformation $\mathbf{F}$ do not change $\Psi$.

**Linear elasticity** features a quadratic energy density function and is specifically designed for **infinitesimal strains** $\bm{\epsilon}$, lacking rigid modes in its null space. Yet, with the corresponding **Lame Parameters** $\mu$ and $\lambda$, it can accurately capture behaviors of small deformations observed in the real world. Standard elasticity models are required to be **consistent with linear elasticity** under small deformations.

This lecture focused on **isotropic elasticity**, where no special directions exist that make the material harder or easier to deform. Performing **Polar SVD** on $\mathbf{F} = \mathbf{U}\bm{\Sigma}\mathbf{V}$ allows us to rewrite $\Psi(\mathbf{F})$ of isotropic models using only **principal stretches** $\sigma_i = \bm{\Sigma}_{ii}$.

Using the **closest rotation** $\mathbf{R}^n = \mathbf{U}^n(\mathbf{V}^n)^T$ to $\mathbf{F}^n$ in the last time step, we constructed a **corotated linear elasticity** to make linear elasticity rotation-aware while maintaining its simplicity. Simplifying further by retaining only the $\mu$-term enhances efficiency for visual computing.

Similar to how non-interpenetrations are enforced in IPC, the energy density function of Neo-Hookean acts as a barrier function, ensuring non-inversion ($\det(\mathbf{F}) > 0$). All other elasticity models introduced in this lecture are **invertible**, and they do not guarantee non-inversion.

In the next lecture, we will explore the derivatives of $\Psi(\mathbf{F})$ with respect to $\mathbf{F}$.
