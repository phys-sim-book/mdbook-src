## Discrete Time


Discretization in time links $\mathbf{A}$ to our degrees of freedom (DOF) $\mathbf{x}$. In the continuous setting, $\mathbf{A}(\mathbf{X}, t) = \frac{\partial^2 \mathbf{x}}{\partial t^2}(\mathbf{X}, t)$. Now, let us divide time into small intervals, $t^0, t^2, \ldots, t^n, \ldots$, as discussed in the first chapter. Using the finite difference formula, we can conveniently approximate $\mathbf{A}$ in terms of $\mathbf{x}$.


For example, with backward Euler:
$$
\begin{aligned}
\mathbf{A}^n(\mathbf{X}) &= \frac{\mathbf{V}^n(\mathbf{X}) - \mathbf{V}^{n-1}(\mathbf{X})}{t^n - t^{n-1}}, \\
\mathbf{V}^n(\mathbf{X}) &= \frac{\mathbf{x}^n(\mathbf{X}) - \mathbf{x}^{n-1}(\mathbf{X})}{t^n - t^{n-1}},
\end{aligned}
$$
which gives us:
$$
\mathbf{A}^n(\mathbf{X}) = \frac{\mathbf{x}^n(\mathbf{X}) - (\mathbf{x}^{n-1}(\mathbf{X}) + \Delta t \mathbf{V}^{n-1}(\mathbf{X}))}{\Delta t^2},
$$
where $\Delta t = t^n - t^{n-1}$. Applying this relation at the sample points into Equation {{eqref: eq:lec17:test_func_chosen}}, we obtain:
$$
\begin{aligned}
& M_{\hat{a}b} \frac{x^n_{b|\hat{i}} - (x^{n-1}_{b|\hat{i}} + \Delta t V^{n-1}_{b|\hat{i}})}{\Delta t^2} \\
&= \int_{\partial\Omega^0} N_{\hat{a}}(\mathbf{X}) T_{\hat{i}}(\mathbf{X}, t^n) ds(\mathbf{X}) - \int_{\Omega^0} N_{\hat{a},j}(\mathbf{X}) P_{\hat{i}j}(\mathbf{X}, t^n) d\mathbf{X}.
\end{aligned}
{{numeq}}{eq:lec17:BE}
$$

Then, by applying mass lumping and zero traction boundary conditions, i.e., $\mathbf{T}(\mathbf{X}, t) = \mathbf{0}$, we finally see that Equation {{eqref: eq:lec17:BE}} is the $(\hat{a}d+\hat{i})$-th row of the discrete form of backward Euler time integration in [the first lecture](./lec1-discrete_space_time.md):
$$
M (\mathbf{x}^{n+1} - (\mathbf{x}^n + \Delta t \mathbf{v}^n)) - \Delta t^2 f(\mathbf{x}^{n+1}) = 0,
$$
where the elasticity force $f(\mathbf{x})$ is obtained by evaluating:
$$
-\int_{\Omega^0} N_{\hat{a},j}(\mathbf{X}) P_{\hat{i}j}(\mathbf{X}, t) d\mathbf{X},
$$
which will be discussed in the next chapter.
