## Stress

Stress is a tensor field, akin to the deformation gradient $\mathbf{F}$, and is defined over the entire domain of solid materials. It quantifies the internal pressures and tensions experienced by a material object. The link between stress and strain (or $\mathbf{F}$) is established through what is known as a **constitutive relationship**. This relationship outlines how materials respond to various deformations.

A common example of a constitutive relationship is Hooke's law in one dimension, which applies to many conventional materials under elastic conditions. In the context of **hyperelastic materials**, the relationship is specifically defined by the strain energy function, $\Psi(\mathbf{F})$. 

> **{{def}}{def:lec13:hyperelastic}[Hyperelastic Materials]**
> Hyperelastic materials are those elastic solids whose **first Piola-Kirchhoff stress** $\mathbf{P}$
can be derived from a strain energy density function $\Psi(\mathbf{F})$ via
$$
\mathbf{P} = \frac{\partial \Psi}{\partial \mathbf{F}}. {{numeq}}{eqn:stress}
$$
> With index notation, this means 
$$
P_{ij} = \frac{\partial \Psi}{\partial F_{ij}}.
$$
> $\mathbf{P}$ is discretely a small matrix with the same dimensions as $\mathbf{F}$.


In the study of material behavior under stress, various definitions are utilized, with **Cauchy stress** being particularly prevalent in engineering contexts. Cauchy stress, denoted as $\sigma(\cdot,t):\Omega^t\rightarrow\mathbb{R}^{d\times d}$, can be mathematically linked to the first Piola-Kirchhoff stress tensor $\mathbf{P}$ through the relationship:
$$
\sigma = \frac{1}{J} \mathbf{P} \mathbf{F}^T = \frac{1}{\text{det}(\mathbf{F})} \frac{\partial \Psi}{\partial \mathbf{F}} \mathbf{F}^T.
$$

Calculating $\mathbf{P}$ from the strain energy function $\Psi(\mathbf{F})$ is relatively straightforward for energy models that do not require singular value decomposition (SVD), such as the Neo-Hookean model. However, general isotropic elasticity models, like ARAP (As-Rigid-As-Possible), often rely on the computation of principal stretches or the closest rotation matrix, necessitating SVD. This computation becomes particularly complex and resource-intensive when determining $\frac{\partial \mathbf{P}}{\partial \mathbf{F}}$, which is crucial for implicit time integrations.

We present an efficient method that leverages the sparsity structure, as introduced by {{#cite stomakhin2012energetically}}, to compute the first Piola-Kirchhoff stress tensor $\mathbf{P}$ and its derivative $\frac{\partial \mathbf{P}}{\partial \mathbf{F}}$ (whether as a tensor or the differential $\delta \mathbf{P}$) for general isotropic elastic materials. This approach utilizes symbolic software packages, and we will specifically discuss the implementation in *Mathematica*. Implementations in *Maple* or other software are similarly straightforward, following the same conceptual framework. For a deeper exploration of derivative computations commonly employed in computer graphics, refer to the work of {{#cite schroeder2022practical}}.

It is important to note that the computational strategy discussed can also be applied to other derivatives in diagonal space, similar to $\frac{\partial \mathbf{P}}{\partial \mathbf{F}}$. For instance, in certain models, the **Kirchhoff stress** $\tau$ is preferred over the first Piola-Kirchhoff stress $\mathbf{P}$. The Kirchhoff stress is expressed as:
$$
\tau = \mathbf{U} \hat{\tau} \mathbf{U}^T,
$$
where $\hat{\tau}$ is a diagonal stress measure, with each entry being a function of the singular values $\Sigma$. The methodology for computing $\frac{\partial \tau}{\partial \mathbf{F}}$ mirrors that of $\mathbf{P}$.
