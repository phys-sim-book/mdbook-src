# Linear Finite Elements

From the governing equations in the continuous setting, we derived the discretized weak form system (nd equations) using the backward Euler time integration rule:

$$
\begin{aligned}
   & M_{\hat{a}b} \frac{x^n_{b|\hat{i}} - (x^{n-1}_{b|\hat{i}} + h V^{n-1}_{b|\hat{i}})}{\Delta t^2} \\
   & = \int_{\partial \Omega^0} N_{\hat{a}}(\mathbf{X}) T_{\hat{i}}(\mathbf{X}, t^n) \, ds(\mathbf{X}) - \int_{\Omega^0} N_{\hat{a}, j}(\mathbf{X}) P_{\hat{i}j}(\mathbf{X}, t^n) \, d\mathbf{X}.
\end{aligned}
{{numeq}}{eq:lec19:discrete_weak_form_BE}
$$
In this chapter, we'll start by discussing the shape function $N_{\hat{a}}$ in the context of linear finite elements. This exploration will help us understand the underlying implementation detailed in [Inversion-Free Elasticity](./lec15-inv_free_elasticity.md).

We'll focus specifically on simplex finite elements. In 2D, the 2-simplex is a triangle, and we've consistently used triangle meshes throughout this book to discretize the solid domain into a disjoint set of triangular elements.

> **{{def}}{def:lec19:simplex}[Simplex]**  
> An n-simplex is a geometric object with $n + 1$ vertices that exists in an $n$-dimensional space. It cannot fit in any space of smaller dimension.