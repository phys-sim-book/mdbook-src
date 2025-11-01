# The Continuous Cosserat Rod Model

What does it mean to model a rod in the continuous world? The Cosserat model describes a rod's configuration in 3D space using two components, both parameterized by some variable $s$:
1.  A **centerline curve** $\mathbf{r}(s): [0,L] \rightarrow \mathbb{R}^3$, defining the rod's position, where $s \in [0,L]$ and $L$ is the total length of the rod.
2.  An **orientation** $\mathbf{q}(s)$, represented by a unit quaternion, defining the material's reference frame at each point on the centerline.

This material frame consists of three orthonormal basis vectors, or **directors** $\{\mathbf{d}_1(s), \mathbf{d}_2(s), \mathbf{d}_3(s)\}$, which are obtained by rotating the standard basis vectors $\{\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3\}$ by the quaternion $\mathbf{q}(s)$. The directors $\mathbf{d}_1$ and $\mathbf{d}_2$ span the rod's cross-section, while $\mathbf{d}_3$ is the normal to the cross-section, typically aligned with the rod's tangent in its rest state.


With this representation, how can we mathematically define the rod's deformation? We do so through two primary strain measures.

#### Stretching and Shearing Strain

In a relaxed, undeformed state, the tangent to the centerline, $\partial_s \mathbf{r}(s)$, should be aligned with the material's primary axis, $\mathbf{d}_3(s)$. The strain is the difference between these two vectors. This gives us the **stretching and shearing strain vector**, $\boldsymbol{\Gamma}(s)$:
$$
{{numeq}}{eq:continuous:stretch_strain}
\boldsymbol{\Gamma}(s) = \partial_s \mathbf{r}(s) - \mathbf{d}_3(s)
$$
This vector becomes non-zero if the rod is stretched or sheared {{#cite kugelstadt2016position}}.

#### Bending and Twisting Strain

The second deformation type arises from the change in orientation along the curve. This "spatial rate of change" of the material frame is captured by the **Darboux vector**, $\boldsymbol{\Omega}(s)$, which measures the rod's curvature (bending) and torsion (twisting). 

The Darboux vector is formally defined as:
$$
{{numeq}}{eq:continuous:darboux_vector_definition}
\boldsymbol{\Omega}(s) = \frac{1}{2} \sum_{k=1}^{3} \mathbf{d}_k(s) \times \frac{\partial \mathbf{d}_k(s)}{\partial s}.
$$
This definition captures how the material frame rotates as we move along the curve parameter $s$. It can be shown that this definition is equivalent to the relationship:
$$
{{numeq}}{eq:continuous:darboux_vector_derivative}
\frac{\partial \mathbf{d}_k(s)}{\partial s} = \boldsymbol{\Omega}(s) \times \mathbf{d}_k(s)
$$
This shows that the Darboux vector describes the rate of change of the directors when the curve parameter $s$ is varied, analogous to how angular velocity describes the rate of change of basis vectors with respect to time.

In terms of our quaternion representation, it can be proven that the Darboux vector has a very form:
$$
{{numeq}}{eq:continuous:darboux_vector}
\boldsymbol{\Omega}(s) = \mathfrak{I}\left(2\overline{\mathbf{q}}(s) \frac{\partial \mathbf{q}(s)}{\partial s}\right)
$$
where $\overline{\mathbf{q}}$ is the quaternion conjugate and $\mathfrak{I}(\cdot)$ extracts the vector part. The components of $\boldsymbol{\Omega}(s)$ in the material frame correspond directly to the bending curvatures about $\mathbf{d}_1$ and $\mathbf{d}_2$ and the axial twist about $\mathbf{d}_3$ {{#cite kugelstadt2016position}}.

#### Continuous Potential Energy

To find the total energy, we must integrate the energy density along the entire length of the rod. The infinitesimal element of arc length is given by $\|\partial_s \mathbf{r}(s)\| ds$. The total potential energy is therefore the integral of the squared strains, weighted by stiffness, over the arc length of the curve.

But what are the stiffness values, and where do they come from? To make our simulation physically meaningful, the potential energies are weighted by stiffness matrices that depend on the rod's material and geometry. These matrices are defined in terms of:

* **Young's Modulus ($E$)** and **Shear Modulus ($G$)**. The shear modulus is related to Young's modulus via the material's Poisson's ratio, $\nu$, as $G = E / (2(1+\nu))$.
* **Cross-section Area Components ($A_1, A_2, A_3$)** and **Moments of Inertia ($J_1, J_2, J_3$)**.

The general form of the stiffness matrices for an **anisotropic** material (one with different properties in different directions) is:
$$
{{numeq}}{eq:continuous:stiffness_matrices}
C^{\Gamma} = \text{diag}(GA_1, GA_2, EA_3) \quad \text{and} \quad C^{\Omega} = \text{diag}(EJ_1, EJ_2, GJ_3)
$$
For the common case of an **isotropic** rod with a circular cross-section of radius $r$, the area is uniform ($A = \pi r^2$), and the moments of inertia are $J_1 = J_2 = \frac{\pi r^4}{4}$ (bending inertia) and $J_3 = J_1 + J_2 = \frac{\pi r^4}{2}$ (polar/torsional inertia) {{#cite kugelstadt2016position}}.

The total potential energy is the integral of these weighted, squared strains. In its general matrix form, this is:
$$
{{numeq}}{eq:continuous:total_energy_matrix}
E^{total} = \frac{1}{2} \int \left( \boldsymbol{\Gamma}(s)^T C^{\Gamma} \boldsymbol{\Gamma}(s) + (\boldsymbol{\Omega}(s) - \boldsymbol{\Omega}^0(s))^T C^{\Omega} (\boldsymbol{\Omega}(s) - \boldsymbol{\Omega}^0(s)) \right) \|\partial_s \mathbf{r}(s)\| ds
$$
Alternatively, for simpler isotropic materials, this is often written using scalar stiffness parameters $k^{ss}$ and $k^{bt}$:
$$
{{numeq}}{eq:continuous:total_energy_scalar}
E^{total} = \int \left( \frac{k^{ss}}{2} \|\boldsymbol{\Gamma}(s)\|^2 + \frac{k^{bt}}{2} \|\boldsymbol{\Omega}(s) - \boldsymbol{\Omega}^0(s)\|^2 \right) \|\partial_s \mathbf{r}(s)\| ds
$$

Conceptually, these integrals define the total elastic potential energy of the rod, creating an energy landscape that our simulation will seek to minimize. The first term, governed by $\boldsymbol{\Gamma}$, penalizes any stretching or shearing of the centerline, while the second term, governed by $\Delta\boldsymbol{\Omega}$, penalizes any bending or twisting away from the rod's natural rest shape.