## Model Reduction for Nonlinear Systems

Linear modal analysis is remarkably efficient, but its underlying assumption of small deformations is a major limitation. For simulating visually rich phenomena like buckling, large bending, or twisting, a linear model produces severe visual artifacts. As shown in the previous section, a simple linear model fails to capture the natural foreshortening that occurs when an object bends, resulting in an unrealistic conservation of length and loss of volume. To correctly capture these essential nonlinear effects, we must apply model reduction to the full nonlinear equations of motion.

### Projecting the Nonlinear Equations

The equation of motion for a general hyperelastic object, including damping, is:

$$ \mathbf{M}\ddot{\mathbf{u}} + \mathbf{D}\dot{\mathbf{u}} + \mathbf{f}_{int}(\mathbf{u}) = \mathbf{f}_{ext} \quad (3.1) $$

Here, the linear elastic force $-\mathbf{K}\mathbf{u}$ is replaced by a general **internal force function** $\mathbf{f}_{int}(\mathbf{u})$, which can be a complex nonlinear function of the displacements $\mathbf{u}$. This nonlinearity can arise from the material model (stress-strain relationship) or from the strain measure itself (geometric nonlinearity).

We follow the same projection-based procedure as in the linear case. We assume the existence of a suitable basis matrix $\mathbf{U} \in \mathbb{R}^{3n \times r}$ (where $r \ll 3n$)—more on the choice of basis in the next chapter—and approximate the full-space displacements $\mathbf{u}$ using the reduced coordinates $\mathbf{q} \in \mathbb{R}^r$:

$$ \mathbf{u}(t) \approx \mathbf{U}\mathbf{q}(t) $$

Substituting this into Equation 3.1 and pre-multiplying by $\mathbf{U}^T$ to project the dynamics onto the subspace yields the **reduced nonlinear equation of motion**:

$$ \mathbf{U}^T\mathbf{M}\mathbf{U}\ddot{\mathbf{q}} + \mathbf{U}^T\mathbf{D}\mathbf{U}\dot{\mathbf{q}} + \mathbf{U}^T\mathbf{f}_{int}(\mathbf{Uq}) = \mathbf{U}^T\mathbf{f}_{ext}
{{numeq}}{eq:lec26:nonlinear_system}
$$

Assuming we have constructed a mass-orthonormal basis such that $\mathbf{U}^T\mathbf{M}\mathbf{U} = \mathbf{I}$, this simplifies to:

$$ \ddot{\mathbf{q}} + \hat{\mathbf{D}} \dot{\mathbf{q}} + \hat{\mathbf{f}}_{int}(\mathbf{q}) = \hat{\mathbf{f}}_{ext}
{{numeq}}{eq:lec26:simplified_nonlienar}
$$

where:
*   $\hat{\mathbf{D}} = \mathbf{U}^T\mathbf{D}\mathbf{U}$ is the $r \times r$ reduced damping matrix.
*   $\hat{\mathbf{f}}_{int}(\mathbf{q}) = \mathbf{U}^T\mathbf{f}_{int}(\mathbf{Uq})$ is the $r \times 1$ **reduced internal force** vector.
*   $\hat{\mathbf{f}}_{ext} = \mathbf{U}^T\mathbf{f}_{ext}$ is the $r \times 1$ reduced external force vector.


Unlike the linear case, the reduced system in {{eqref:eq:lec26:nonlinear_system}} is no longer a set of independent 1D oscillators. The reduced internal force $\hat{\mathbf{f}}_{int}(\mathbf{q})$ is a nonlinear function that couples all the components of $\mathbf{q}$. This introduces two critical questions:
1.  How do we efficiently time-step this coupled, nonlinear system?
2.  How do we choose an effective basis $\mathbf{U}$ that can represent nonlinear deformations? (next chapter)

### Timestepping and the Evaluation Bottleneck

To solve Equation 3.3, we typically use an implicit time integration scheme, which is necessary to handle the high-frequency stiffness common in elastic systems. An implicit step requires solving a linear system involving the derivative of the forces. For our reduced system, this means we need the **reduced tangent stiffness matrix**, $\hat{\mathbf{K}}(\mathbf{q})$:

$$ \hat{\mathbf{K}}(\mathbf{q}) = \frac{\partial \hat{\mathbf{f}}_{int}(\mathbf{q})}{\partial \mathbf{q}} $$

Using the chain rule, we can relate this to the full-space tangent stiffness matrix $\mathbf{K}(\mathbf{u}) = \frac{\partial \mathbf{f}_{int}(\mathbf{u})}{\partial \mathbf{u}}$:

$$ \hat{\mathbf{K}}(\mathbf{q}) = \frac{\partial}{\partial \mathbf{q}} \left( \mathbf{U}^T\mathbf{f}_{int}(\mathbf{Uq}) \right) = \mathbf{U}^T \frac{\partial \mathbf{f}_{int}(\mathbf{Uq})}{\partial \mathbf{q}} = \mathbf{U}^T \left( \frac{\partial \mathbf{f}_{int}(\mathbf{u})}{\partial \mathbf{u}} \bigg|_{\mathbf{u}=\mathbf{Uq}} \frac{\partial (\mathbf{Uq})}{\partial \mathbf{q}} \right) $$

$$ \hat{\mathbf{K}}(\mathbf{q}) = \mathbf{U}^T \mathbf{K}(\mathbf{Uq}) \mathbf{U}$$

At each time step, an implicit integrator will solve a dense $r \times r$ linear system involving $\hat{\mathbf{K}}$. Since $r \ll 3n$, this is a monumental improvement over solving the original sparse $3n \times 3n$ system.

However, a major computational challenge emerges: how do we compute $\hat{\mathbf{f}}_{int}(\mathbf{q})$ and $\hat{\mathbf{K}}(\mathbf{q})$ efficiently? The naive approach is to:
1.  Take the current reduced coordinates $\mathbf{q}$.
2.  **De-project** to find the full-space deformation: $\mathbf{u} = \mathbf{Uq}$.
3.  **Evaluate** the full-space forces $\mathbf{f}_{int}(\mathbf{u})$ and stiffness $\mathbf{K}(\mathbf{u})$ by looping over all elements in the high-resolution mesh.
4.  **Project back** to the reduced space: $\hat{\mathbf{f}}_{int} = \mathbf{U}^T\mathbf{f}_{int}$ and $\hat{\mathbf{K}} = \mathbf{U}^T\mathbf{K}\mathbf{U}$.

This process is extremely slow because step 3 still requires a full pass over the high-resolution mesh, completely defeating the purpose of model reduction.

### Accelerating Force Evaluation

To make nonlinear model reduction practical, we need a way to evaluate the reduced forces and stiffness without ever forming their full-space counterparts.

#### Polynomial Fitting (for specific materials)
For certain material models, a highly efficient analytical approach is possible. For instance, in a geometrically nonlinear but materially linear model (e.g., St. Venant-Kirchhoff), each component of the full-space internal force vector $\mathbf{f}_{int}(\mathbf{u})$ is a cubic polynomial in the components of $\mathbf{u}$.

Since $\mathbf{u} = \mathbf{Uq}$, it follows that the reduced force $\hat{\mathbf{f}}_{int}(\mathbf{q}) = \mathbf{U}^T\mathbf{f}_{int}(\mathbf{Uq})$ is also a cubic polynomial, but in the reduced coordinates $\mathbf{q}$. We can precompute the coefficients of this $r$-variate cubic polynomial. At runtime, evaluating $\hat{\mathbf{f}}_{int}(\mathbf{q})$ and its derivative $\hat{\mathbf{K}}(\mathbf{q})$ simply involves evaluating these precomputed polynomials. The evaluation cost is $O(r^4)$ for the forces and $O(r^3)$ for implicit integration, which is manageable for small $r$ (e.g., $r < 30$) and completely independent of the mesh size $n$. Unfortunately, this approach is limited to materials where forces are low-degree polynomials.



#### Cubature (for general materials)
A more general and powerful solution is **cubature**. The core insight is to re-examine how the reduced force is calculated.

The reduced energy $\hat{E}$ is the full energy evaluated within the subspace:
$$ \hat{E}(\mathbf{q}) = E(\mathbf{Uq}) = \int_{\Omega_0} \Psi(\mathbf{F}(\mathbf{Uq})) \, dV $$

The reduced internal force $\hat{\mathbf{f}}_{int}$ is the gradient of this reduced energy with respect to the reduced coordinates $\mathbf{q}$. By moving the derivative inside the integral, we find:
$$ \hat{\mathbf{f}}_{int}(\mathbf{q}) = \frac{\partial \hat{E}}{\partial \mathbf{q}} = \int_{\Omega_0} \frac{\partial \Psi(\mathbf{F}(\mathbf{Uq}))}{\partial \mathbf{q}} \, dV  $$
This shows that the reduced force is the integral of the *reduced energy density gradient* over the object's volume. The projection $\mathbf{U}^T$ is implicitly included within the derivative $\partial / \partial \mathbf{q}$ via the chain rule.

Instead of computing this integral exactly by summing contributions from all finite elements, cubature approximates it with a weighted sum over a very small number, $T$, of pre-selected sample points (or "quadrature points") $\mathbf{X}_j \in \Omega_0$:

$$ \hat{\mathbf{f}}_{int}(\mathbf{q}) \approx \sum_{j=1}^{T} w_j \frac{\partial \Psi(\mathbf{F}(\mathbf{Uq}))}{\partial \mathbf{q}} \bigg|_{\mathbf{X}=\mathbf{X}_j}
 {{numeq}}{eq:lec25:cuabture_force}$$

The number of cubature points $T$ can be surprisingly small (often on the order of $r$) while still yielding a highly accurate approximation. The points $\mathbf{X}_j$ and their non-negative weights $w_j$ are optimized in a precomputation step to best match the true reduced forces over a set of representative "training" poses.

At runtime, evaluating the reduced forces and stiffness only requires looping over these $T$ points. The cost becomes independent of the original mesh resolution $n$, making fast simulation of complex, nonlinear materials feasible.


You can notice that it might be very important _how_ we choose the basis $\mathbf{U}$ s.t. we can most effecitvely represent the space of non-linear deformations. This will be discussed in the following chapter!