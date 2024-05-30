## Mass-Spring Potential Energy

In this case study, we focus exclusively on incorporating the mass-spring elasticity potential into our system. The concept of mass-spring elasticity is akin to treating each edge of the mesh as if it were a spring. This approach is inspired by Hooke's Law, allowing us to formulate the potential energy on edge $e$ as follows:

$$
    P_e(x) = l^2 \frac{1}{2} k \left(\frac{\| \mathbf{x}_1 - \mathbf{x}_2 \|^2}{l^2} - 1\right)^2,
    {{numeq}}{eq:lec4:mass_sprint_E}
$$

Here, $\mathbf{x}_1$ and $\mathbf{x}_2$ represent the current positions of the two endpoints of the edge. The variable $l$ denotes the original length of the edge, and $k$ is a parameter controlling the spring's stiffness. Notably, when the distance between the two endpoints $\| \mathbf{x}_1 - \mathbf{x}_2 \|$ equals the original length $l$, the potential energy $P_e(x)$ attains its global minimum value of $0$, indicating no force is exerted.

An important aspect of this formulation is the inclusion of $l^2$ at the beginning. This is analogous to integrating the spring energy across the solid and choosing edges as quadrature points. This integration helps maintain a consistent relationship between the stiffness behavior and the parameter $k$, regardless of mesh resolution variations.

Another deviation from standard spring energy formulations is our avoidance of the square root operation. We directly use $\| \mathbf{x}_1 - \mathbf{x}_2 \|^2$, making our model polynomial in nature. This simplification yields more streamlined expressions for the gradient and Hessian:

$$
    \frac{\partial P_e}{\partial \mathbf{x}_1} (x) = -\frac{\partial P_e}{\partial \mathbf{x}_2} (x) = 2 k \left(\frac{\| \mathbf{x}_1 - \mathbf{x}_2 \|^2}{l^2} - 1\right) (\mathbf{x}_1 - \mathbf{x}_2),
$$

$$
\begin{aligned}
    \frac{\partial^2 P_e}{\partial \mathbf{x}_1^2} (x) & = \frac{\partial^2 P_e}{\partial \mathbf{x}_2^2} (x) = -\frac{\partial^2 P_e}{\partial \mathbf{x}_1\mathbf{x}_2} (x) = -\frac{\partial^2 P_e}{\partial \mathbf{x}_2\mathbf{x}_1} (x) \\ & = \frac{4 k}{l^2} (\mathbf{x}_1 - \mathbf{x}_2) (\mathbf{x}_1 - \mathbf{x}_2)^T + 2 k \left(\frac{\| \mathbf{x}_1 - \mathbf{x}_2 \|^2}{l^2} - 1\right) \mathbf{I} \\
    & = \frac{2k}{l^2} \left(2(\mathbf{x}_1 - \mathbf{x}_2) (\mathbf{x}_1 - \mathbf{x}_2)^T + (\| \mathbf{x}_1 - \mathbf{x}_2 \|^2 - l^2)\mathbf{I}\right).
\end{aligned}
$$

The total potential energy of the system, denoted as $P(x)$, can be derived by summing the potential energy across all edges. This is calculated using Equation {{eqref: eq:lec4:mass_sprint_E}}. Thus, the total potential energy is expressed as:
$$
    P(x) = \sum_e P_e(x)
$$
where the summation is taken over all edges in the mesh.

{{imp}}{imp:lec4:mass_spring_energy}[MassSpringEnergy.py]
```python
{{#include solid-sim-tutorial/1_mass_spring/MassSpringEnergy.py}}
```

In dealing with the Hessian matrix of the mass-spring energy, a key consideration is its non-symmetric positive definite (SPD) nature. To address this, a specific modification is employed: we neutralize the negative eigenvalues of the local Hessian corresponding to each edge. This is done prior to incorporating these local Hessians into the global matrix. The process involves setting negative eigenvalues to zero, thus ensuring that the resulting global Hessian matrix adheres more closely to the desired SPD properties. This modification is crucial for Newton's method.

{{imp}}{imp:lec4:utils}[Positive Semi-Definite Projection]
```python
{{#include solid-sim-tutorial/1_mass_spring/utils.py}}
```