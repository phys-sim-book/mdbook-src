## Polar Singular Value Decomposition

When discussing [general slip boundary conditions](lec6.3-general_slip_DBC.md), we introduced the usage of singular value decomposition (SVD). Here, we apply a variant known as **Polar SVD** ({{ref: alg:lec13:polar_SVD}}) to decompose $\mathbf{F}$:
$$
    \mathbf{F} = \mathbf{U} \mathbf{\Sigma} \mathbf{V}^T,
$$
where $\mathbf{U}$ and $\mathbf{V}$ are both $d\times d$ rotation matrices, and $\mathbf{\Sigma}$ is a $d \times d$ diagonal matrix. Unlike standard SVD, which ensures $\mathbf{\Sigma}_{ii}$ remains non-negative possibly at the expense of having $\det(\mathbf{U}) = -1$ or $\det(\mathbf{V}) = -1$, Polar SVD maintains $\det(\mathbf{U}) = 1$ and $\det(\mathbf{V}) = 1$, allowing $\mathbf{\Sigma}_{ii}$ to be negative if necessary.

Polar SVD is named for its relation to Polar decomposition, where $\mathbf{F}$ is expressed as $\mathbf{R}\mathbf{S}$. This decomposition can be reconstructed via $\mathbf{R} = \mathbf{U}\mathbf{V}^T$ and $\mathbf{S} = \mathbf{V}\mathbf{\Sigma}\mathbf{V}^T$, with $\mathbf{R}$ representing the closest rotation to $\mathbf{F}$ and $\mathbf{S}$ being symmetric.

<figure>
    <center>
    <b><figcaption>{{alg}}{alg:lec13:polar_SVD}[Polar SVD from Standard SVD]</figcaption></b>
    <img src="img/lec13/polar_svd_algorithm.jpg" width="610">
    </center>
</figure>

The Polar SVD of $\mathbf{F}$ offers a more intuitive way to understand deformation. If we denote $\sigma_i = \mathbf{\Sigma}_{ii}$, referred to as the **principal stretches**, we can conceptualize $\mathbf{F}$ as comprising a sequence of transformations. Initially, there is a rotation by $\mathbf{V}^T$, followed by scaling the dimensions by $\sigma_i$ along each axis, and concluding with another rotation by $\mathbf{U}$. This decomposition is applicable for all possible $\mathbf{F}$.


Polar SVD also allows for the more convenient expression of isotropic strain energy density functions using $\sigma_i$ exclusively. For instance, our intuitive formulation in Equation {{eqref: eq:lec13:strain_density_intuitive}} can be reframed as:

$$
\Psi(\mathbf{F}) = \hat{\Psi}(\mathbf{\Sigma}) = \frac{\mu}{4}\sum_{i=1}^d (\sigma_{i}^2 - 1)^2 + \frac{\lambda}{2}\left(\prod_{i=1}^d \sigma_i - 1\right)^2,
$$

where $J = \prod_{i=1}^d \sigma_i = \sigma_1 \cdot \sigma_2 \cdot \ldots \cdot \sigma_d$. Moreover, the Neo-Hookean strain energy density function (Equation {{eqref: eq:lec13:strain_density_NH}}) can be rewritten as:

$$
\Psi_\text{NH}(\mathbf{F}) = \hat{\Psi}_\text{NH}(\mathbf{\Sigma}) = \frac{\mu}{2} \left(\sum_{i=1}^d \sigma_{i}^2 - d\right) -\mu\ln(J)+\frac{\lambda}{2}\ln^2(J).
$$

These two models are both consistent with linear elasticity under small deformation.

> **{{def}}{def:lec13:consistency_linear_elasticity}[Consistency to Linear Elasticity]**
> To verify the consistency to linear elasticity of a strain energy density function $\Psi(\mathbf{F})$, we just need to check whether the following relations all hold:
$$
    \hat{\Psi}(\mathbf{I}) = 0, \quad
    \frac{\partial \hat{\Psi}}{\partial \sigma_i}(\mathbf{I}) = 0, \quad \text{and} \quad 
    \frac{\partial^2 \hat{\Psi}}{\partial \sigma_i \partial \sigma_j}(\mathbf{I}) = 2\mu\delta_{ij} + \lambda.
$$
> Here $1 \leq i,j \leq d$, and $\delta_{ij} = 1$ if $i=j$, otherwise it is $0$.
