# Discretization of Weak Forms

In this lecture, we will discretize the weak form of the momentum conservation equation (temporarily ignoring body forces) in both space and time to reach the discrete form—a system of equations introduced in [the first lecture](./lec1-discrete_space_time.md).

We will begin by focusing on a specific point in time, $t = t^n$. From the weak form of the momentum conservation equation (Equation {{eqref: eq:lec16:weakform_lagrangian}}), we have:
$$
\begin{aligned}
& \int_{\Omega^0} R^0(\mathbf{X}) Q^n_i(\mathbf{X}) A^n_i(\mathbf{X}) d\mathbf{X} \\
&= \int_{\partial \Omega^0} Q^n_i(\mathbf{X}) T^n_i(\mathbf{X}) ds(\mathbf{X}) - \int_{\Omega^0} Q^n_{i,j}(\mathbf{X}) P^n_{ij}(\mathbf{X}) d\mathbf{X},
{{numeq}}{eq:lec17:weakform_lagrangian_tn}
\end{aligned}
$$
for arbitrary $\mathbf{Q}^n(\mathbf{X})$, where the superscript $n$ denotes quantities measured at $t = t^n$. Here:
- $R$ and $\mathbf{T}$ are specified by the simulation setup,
- $\mathbf{P}$ can be calculated from the degrees of freedom $\mathbf{x}$ via a constitutive law,
- $\mathbf{A} = \frac{\partial^2 \mathbf{x}}{\partial t^2}$ is the second-order time derivative of $\mathbf{x}$, and
- $\mathbf{Q}$ is an arbitrary vector field.
