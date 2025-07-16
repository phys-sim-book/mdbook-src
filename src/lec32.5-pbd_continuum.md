## Continuum Mechanics-Based Constraints

While simple geometric constraints such as distance and volume are effective for many materials, a more physically rigorous and expressive simulation can be achieved by deriving constraints directly from the principles of continuum mechanics. This approach connects Position-Based Dynamics to the rich theoretical foundation of established methods like the Finite Element Method (FEM), allowing for the simulation of complex material behaviors including anisotropy, elastoplasticity, and nonlinear elasticity. 
The core idea is to define constraints based on the deformation gradient, a tensor that measures the local stretching and shearing of the material. 

This section will first review the necessary concepts of discretized [deformation and strain](./lec12-kinematics.md). It will then detail two primary methods for formulating continuum-based constraints: a holistic approach based on [strain energy](./lec13-strain_energy.md) and a more granular method that constrains individual components of the strain tensor directly.


### The Strain Energy Constraint

The most direct way to incorporate a standard hyperelastic material model into PBD is to use its strain energy potential as a constraint function. 

In a discrete setting, such as a tetrahedral mesh, the deformation gradient is assumed to be constant within each element. For a tetrahedron with material-space vertices $(\bm{P}_1, \bm{P}_2, \bm{P}_3, \bm{P}_4)$ and world-space vertices $(\bm{p}_1, \bm{p}_2, \bm{p}_3, \bm{p}_4)$, we can define a material-space shape matrix $\bm{D}_m$ and a world-space shape matrix $\bm{D}_s$ from the edge vectors:
$$
\begin{aligned}
\bm{D}_m &= [\bm{P}_1-\bm{P}_4, \quad \bm{P}_2-\bm{P}_4, \quad \bm{P}_3-\bm{P}_4] \\
\bm{D}_s &= [\bm{p}_1-\bm{p}_4, \quad \bm{p}_2-\bm{p}_4, \quad \bm{p}_3-\bm{p}_4]
\end{aligned}
$$
The deformation gradient for the tetrahedron is then given by $\bm{F} = \bm{D}_s \bm{D}_m^{-1}$. Note that $\bm{D}_m^{-1}$ is constant and can be precomputed.

From $\bm{F}$, we can derive various strain measures. One of the most common is the **Green-St. Venant strain tensor**, $\bm{\varepsilon}$, which is independent of rigid-body rotations:
$$
{{numeq}}{eq:continuum:green_strain}
\bm{\varepsilon} = \frac{1}{2}(\bm{F}^T\bm{F} - \bm{I})
$$
The related **right Cauchy-Green deformation tensor**, $\bm{S} = \bm{F}^T\bm{F} = 2\bm{\varepsilon} + \bm{I}$, will be particularly useful for formulating direct strain constraints. If there is no deformation, $\bm{F}=\bm{I}$, $\bm{S}=\bm{I}$, and $\bm{\varepsilon}=\bm{0}$.

By Hooke’s generalized law, we can relate stress to strain:

$$\bm{S} = \bm{C}\bm{\varepsilon}$$
where $\bm{C}$ is the elasticity tensor of the material. Recall that the energy of a deformed solid is defined by integrating the scalar strain energy density field over the whole body $\Omega$:

$$
E_s =\int_{\Omega} \Psi_s d\bm{P}
$$ 
where the strain energy density can be defined in terms of stress and strain:   
$$
\Psi_s = \frac{1}{2}\varepsilon : \bm{S} = \frac{1}{2}tr(\varepsilon ^\top \bm{S})
$$

So the total strain energy for a single tetrahedral element is its rest volume $V_0$ multiplied by the energy density. For an undeformed state, this energy is zero. This naturally leads to the following constraint formulation:
$$
{{numeq}}{eq:continuum:energy_constraint}
C(\bm{p}_1, ..., \bm{p}_4) = E_s = V_0 \Psi_s(\bm{F}(\bm{p}_1, ..., \bm{p}_4)) = 0
$$


The PBD solver requires the gradient of the constraint with respect to the positions of the vertices. This gradient has a deep physical meaning, as it is directly related to the **first Piola-Kirchhoff stress tensor**, $\bm{P}$, which relates forces in the current configuration to areas in the reference configuration. The stress tensor is a function of the deformation gradient, $\bm{P}(\bm{F})$. Using the chain rule, the gradients of the energy for the first three vertices can be computed in a compact matrix form:
$$
{{numeq}}{eq:continuum:energy_gradient_tet}
\left[ \frac{\partial E_s}{\partial \bm{p}_1}, \frac{\partial E_s}{\partial \bm{p}_2}, \frac{\partial E_s}{\partial \bm{p}_3} \right] = V_0 \bm{P}(\bm{F}) \bm{D}_m^{-T}
$$
The gradient for the fourth vertex is determined by the condition of translational invariance (i.e., the net force on the element must be zero):
$$
{{numeq}}{eq:continuum:energy_gradient_tet4}
\frac{\partial E_s}{\partial \bm{p}_4} = - \sum_{i=1}^{3} \frac{\partial E_s}{\partial \bm{p}_i}
$$
This formulation extends naturally to surface meshes composed of triangles. For a triangle with rest area $A_0$, the constraint is $C(\bm{p}) = A_0 \Psi_s(\bm{F}_{\text{tri}})$. The gradients are computed analogously:
$$
{{numeq}}{eq:continuum:energy_gradient_tri}
\left[ \frac{\partial E_s}{\partial \bm{p}_1}, \frac{\partial E_s}{\partial \bm{p}_2} \right] = A_0 \bm{P}(\bm{F}_{\text{tri}}) \bm{D}_m^{-T} \quad \text{and} \quad \frac{\partial E_s}{\partial \bm{p}_3} = - \sum_{i=1}^{2} \frac{\partial E_s}{\partial \bm{p}_i}
$$

> **{{rem}}{rem:continuum:degenerate_elements}[Handling Element Inversion]**
> Standard constitutive models are often not defined for degenerate or inverted elements (where $\det(\bm{F}) \le 0$), which can cause instabilities in a simulation. This is a critical practical issue. This problem can be robustly addressed by augmenting the constitutive model with techniques designed for inversion handling.

The elegance of this strain energy constraint lies in its generality. It is not limited to a single material type, like the simple St. Venant-Kirchhoff model. More complex and physically accurate models, such as the Neo-Hookean model for rubber-like materials, can be readily supported by simply substituting the appropriate energy density function $\Psi_s$. This allows PBD to simulate complex physical effects like lateral contraction (Poisson's effect), anisotropy, and elastoplasticity with high fidelity and efficiency.



### Direct Strain-Based Constraints

For applications requiring more granular or intuitive control over material properties, particularly for simulating anisotropy, it is advantageous to define separate constraints for each mode of deformation. This approach operates on the components of the right Cauchy-Green deformation tensor, $\bm{S} = \bm{F}^T\bm{F}$.

#### Stretch Constraints
The diagonal entries of $\bm{S}$, $S_{ii}$, represent the squared stretch along the material's reference axes. In an undeformed state, $S_{ii}=1$. To ensure the constraint projection is linear and converges in a single step, the stretch constraint is formulated based on the stretch itself, rather than its square:
$$
{{numeq}}{eq:continuum:linear_stretch_constraint}
C_{\text{stretch}, i}(\bm{x}) = \sqrt{S_{ii}} - 1 = 0
$$
By defining one such constraint for each principal axis, one can assign independent stiffness parameters, which is the key to modeling anisotropic stretch resistance.

#### Shear Constraints
The off-diagonal entries of $\bm{S}$, $S_{ij} = \bm{f}_i \cdot \bm{f}_j$ (where $\bm{f}_k$ are the column vectors of $\bm{F}$), measure the shear between material axes. A naive constraint $S_{ij}=0$ problematically couples shearing and stretching. To isolate the shearing deformation, the constraint must be normalized, thus depending only on the angle between the deformed axes:
$$

{{numeq}}{eq:continuum:decoupled_shear_constraint}
C_{\text{shear}, ij}(\bm{x}) = \frac{\bm{f}_i \cdot \bm{f}_j}{|\bm{f}_i||\bm{f}_j|} = 0
$$
This formulation ensures that the shear constraints enforce orthogonality between the material axes without interfering with the stretch constraints that control their length. This decoupling is critical for intuitive and independent tuning of material behavior.
