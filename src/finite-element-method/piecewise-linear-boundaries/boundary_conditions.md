## Boundary Conditions

### Dirichlet

Due to the accurate boundary resolution of the Finite Element Method (FEM), enforcing Dirichlet boundary conditions is straightforward. We only need to constrain the world-space coordinates of the boundary nodes to the prescribed values:

$$
\hat{\mathbf{x}}(\mathbf{X}_i) = \mathbf{x}_D(\mathbf{X}_i) \quad \forall \ \mathbf{X}_i \in \Gamma_D.
$$

Once these constraints are properly enforced, the Dirichlet boundary integral term can be ignored.

This same mechanism can also be used to prescribe the displacement of any interior nodes. Although this does not directly correspond to any physical effects, it can simplify the simulation setup.

### Neumann

For Neumann boundary conditions, we can evaluate the boundary integral term using the parameter space variables $\beta$ and $\gamma$. With triangle mesh discretization, we have:

$$
\int_{\Gamma_N} N_{\hat{a}}(\mathbf{X}) T_{\hat{i}}(\mathbf{X},t^n) \, ds(\mathbf{X}) = \sum_{e \in \mathcal{T}} \int_{\partial \Omega_e^0 \cap \Gamma_N} N_{\hat{a}}(\mathbf{X}) T_{\hat{i}}(\mathbf{X}, t^n) \, ds(\mathbf{X}),
$$

where $\partial \Omega_e^0 \cap \Gamma_N$ is the edge of triangle $e$ that is on the Neumann boundary.

For any boundary node $\hat{a}$ in 2D, there will be at most two incident triangles to consider in the integration for linear shape functions. Let's examine the case with two incident triangles. Consider one of the integrals. Without loss of generality, assume $N_{\hat{a}} = \beta$ (where $\mathbf{X}_{\hat{a}}$ corresponds to $\mathbf{X}_2$ in triangle $e$), and that $\mathbf{X}_3$ is the other node of $e$ on the boundary edge. Then, switching the integration variables to $\beta$ gives us:

$$
\int_{\partial \Omega_e^0 \cap \Gamma_N} N_{\hat{a}}(\mathbf{X}) T_{\hat{i}}(\mathbf{X}, t^n) \, ds(\mathbf{X}) = \int_0^1 \beta T_{\hat{i}}(\beta \mathbf{X}_2 + (1 - \beta) \mathbf{X}_3, t^n) \left| \frac{\partial s}{\partial \beta} \right| d\beta.
$$

Here, $\left| \frac{\partial s}{\partial \beta} \right|$ is simply the edge length $\|\mathbf{X}_2 - \mathbf{X}_3\|$. If $T$ is constant over the boundary at $t^n$, we can compute:

$$
T^n_{\hat{i}} \int_0^1 \beta \left| \frac{\partial s}{\partial \beta} \right| d\beta = \frac{1}{2} \|\mathbf{X}_2 - \mathbf{X}_3\| T^n_{\hat{i}}.
$$

Therefore, to add a constant Neumann force to the discrete system, we first calculate the length weight of each boundary node by distributing the length of the boundary edges evenly to their vertices, and then multiply by the traction $T_{\hat{i}}^n$. If $\mathbf{T}$ is not constant over the boundary, more complex boundary integral calculations are needed. For a boundary node with only one incident triangle, its length weight comes from its two incident edges within the same triangle.

> **{{rem}}{rem:lec20:neumann_potential}[Neumann Boundary Conditions]** Here, we observe that the specified traction in standard Neumann boundary conditions is independent of $\mathbf{x}$, which simplifies the derivation of the potential energy, even in the continuous setting for varying Neumann forces over the domain:
$$
\int_{\Gamma_N} \mathbf{x}(\mathbf{X}) \cdot \mathbf{T}(\mathbf{X}, t^n) \, ds(\mathbf{X}).
{{numeq}}{eq:lec20:neumann_potential}
$$
To verify this, we can replace $\mathbf{x}(\mathbf{X})$ with $\hat{\mathbf{x}}(\mathbf{X}) = N_{\hat{a}}(\mathbf{X}) \mathbf{x}_{\hat{a}} + \ldots$ for spatial discretization. Taking the derivative with respect to $\mathbf{x}_{\hat{a}}$ gives us the force integral term in the discrete weak form:
$$
\frac{\partial \int_{\Gamma_N} \hat{\mathbf{x}}(\mathbf{X}) \cdot \mathbf{T}(\mathbf{X}, t^n) \, ds(\mathbf{X})}{\partial \mathbf{x}_{\hat{a}}} = \int_{\Gamma_N} N_{\hat{a}}(\mathbf{X}) \mathbf{T}(\mathbf{X}, t^n) \, ds(\mathbf{X}).
$$
