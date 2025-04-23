## Piecewise Linear Displacement Field

For a triangle element with vertices $\mathbf{X}_1$, $\mathbf{X}_2$, and $\mathbf{X}_3$ in the solid domain, we can approximate the world space coordinates of an arbitrary point $\mathbf{X}$ in this element using spatial discretization (see Equation {{eqref: eq:lec17:spatial_discretization}}):

$$
\hat{\mathbf{x}}(\mathbf{X}) = \mathbf{x}(\mathbf{X}_1) N_1(\mathbf{X}) + \mathbf{x}(\mathbf{X}_2) N_2(\mathbf{X}) + \mathbf{x}(\mathbf{X}_3) N_3(\mathbf{X}),
{{numeq}}{eq:lec19:2d_interpolation}
$$

This equation represents a 2D interpolation, extending Experiment {{ref: exp:lec17:1d_interpolation}}. Here, we assume that the world space coordinates of any arbitrary point in an element can be interpolated solely from the coordinates of the element's vertices.

Linear finite elements use linear shape functions $N_i$ in Equation {{eqref: eq:lec19:2d_interpolation}}, resulting in a piecewise linear (per triangle) displacement field $\mathbf{u} = \hat{\mathbf{x}}(\mathbf{X}) - \mathbf{X}$ over the entire domain. Before providing the precise expression of $N$ in terms of $\mathbf{X}$, we'll introduce another parameter space to simplify the derivation.

Let $\beta, \gamma \in [0,1]$ and $\beta + \gamma \leq 1$, we can use them to express the material space coordinates of an arbitrary point $\mathbf{X}$ in the element $\mathbf{X}_1 \mathbf{X}_2 \mathbf{X}_3$ as:

$$
\begin{aligned}
    \mathbf{X}(\beta, \gamma) & = \mathbf{X}_1 + \beta (\mathbf{X}_2 - \mathbf{X}_1) + \gamma (\mathbf{X}_3 - \mathbf{X}_1) \\
    & = (1 - \beta - \gamma) \mathbf{X}_1 + \beta \mathbf{X}_2 + \gamma \mathbf{X}_3.
\end{aligned}
$$

Here, $\mathbf{X}$ is a linear function of $(\beta, \gamma)$. With linear shape functions, the approximation $\hat{\mathbf{x}}$ is a linear function of $\mathbf{X}$.

Recall that for interpolation, we have to satisfy the conditions $\hat{\mathbf{x}}(\mathbf{X}_i) = \mathbf{x}(\mathbf{X}_i)$. Putting these all together, we can obtain a unique solution:

$$
\begin{aligned}
    \mathbf{x}(\beta, \gamma) \approx \hat{\mathbf{x}}(\beta, \gamma) & = \mathbf{x}_1 + \beta (\mathbf{x}_2 - \mathbf{x}_1) + \gamma (\mathbf{x}_3 - \mathbf{x}_1) \\
    & = (1 - \beta - \gamma) \mathbf{x}_1 + \beta \mathbf{x}_2 + \gamma \mathbf{x}_3,
\end{aligned}
$$

where we denote $\mathbf{x}(\mathbf{X}_i)$ as $\mathbf{x}_i$. This indicates that:

$$
N_1(\beta, \gamma) = 1 - \beta - \gamma, \quad N_2(\beta, \gamma) = \beta, \quad N_3(\beta, \gamma) = \gamma.
$$

Interestingly, with the expression of $\mathbf{X}(\beta, \gamma)$, $\mathbf{x}(\beta, \gamma)$, and $N(\beta, \gamma)$, we do not necessarily need the precise expression of $\hat{\mathbf{x}}(\mathbf{X})$ and $N(\mathbf{X})$ for the following derivations to compute each term in Equation {{eqref: eq:lec17:BE}}.

> **{{rem}}{rem:lec19:fem_partition}[Partition of Unity]** The shape functions of FEM satisfy the **partition of unity** everywhere within each element:
$$
N_1(\beta, \gamma) + N_2(\beta, \gamma) + N_3(\beta, \gamma) = 1 \quad \forall \ \beta, \gamma \in [0, 1] \ \text{and} \ \beta + \gamma \leq 1.
$$
> One advantage of FEM is that it provides accurate boundary resolution compared to grid or particle-based representations. The boundary nodes of the FEM mesh can be exactly located on the boundary of the continuous domain. The elements are generated inside the domain, connecting the boundary nodes to form the discrete boundary, which converges to the boundary of the continuous domain as resolution increases.  
> Although particle-based methods can also sample particles on the domain boundary, their spherical shape functions extend beyond the domain, breaking the partition of unity. This creates a "soft" outbound layer of material that makes boundary force computations inaccurate. In contrast, FEM shape functions are nonzero only within each element, where the partition of unity is satisfied everywhere.

