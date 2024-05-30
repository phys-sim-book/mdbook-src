## Discrete Space

To enable numerical evaluation of the integrals in the weak form, the first step is to discretize the smooth vector fields $\mathbf{x}$ and $\mathbf{Q}$. This allows them to be represented by a finite set of samples, along with appropriate interpolation functions.

> **{{exp}}{exp:lec17:1d_interpolation}[1D Function Interpolation]**
> In 1D, to approximate a function $f(x)$ using three sample points $x_1 = 1$, $x_2 = 2$, $x_3 = 3$ ({{ref: fig:lec17:1d_interpolation}}), we can use interpolation functions $N_i(x) = 1 - |x - x_i|$ and form $f(x) \approx \sum_i f(x_i) N_i(x)$. <figure>
    <center>
    <img src="img/lec17/shapeFun1D.jpg" width="550">
    </center>
    <figcaption><b>{{fig}}{fig:lec17:1d_interpolation}</b> With interpolation functions $N_1(x)$, $N_2(x)$, $N_3(x)$ and sample points $x_1 = 1$, $x_2 = 2$, $x_3 = 3$, a function $f(x)$ can be approximated as $\sum_i f(x_i) N_i(x)$. </figcaption>
</figure>

Given a set of sample points indexed by $a$ or $b$ in the simulation domain, we can approximate the test function $\mathbf{Q}$ and the DOF $\mathbf{x}$ as:
$$
\begin{aligned}
Q_i(\mathbf{X}, t^n) &\approx \sum_a Q_{a|i}(t^n) N_a(\mathbf{X}) = \sum_a Q^n_{a|i} N_a(\mathbf{X}), \\
\mathbf{x}_i(\mathbf{X}, t^n) &\approx \sum_b \mathbf{x}_{b|i}(t^n) N_b(\mathbf{X}) = \sum_b \mathbf{x}^n_{b|i} N_b(\mathbf{X}),
\end{aligned}
$$
where $Q^n_{a|i} = Q_{a|i}(t^n)$ refers to the $i$-th dimension of $\mathbf{Q}$ evaluated at sample point $a$ at time $t^n$, and $N_a(\mathbf{X}): \Omega^0 \rightarrow \mathbb{R}$ is the interpolation function at sample point $a$. In this way, we similarly have:
$$
A_i(\mathbf{X}, t^n) \approx \sum_b A_{b|i}(t^n) N_b(\mathbf{X}) = \sum_b A^n_{b|i} N_b(\mathbf{X}).
{{numeq}}{eq:lec17:spatial_discretization}
$$
Plugging these discretizations into the weak form (Equation {{eqref: eq:lec17:weakform_lagrangian_tn}}) and expressing summations with the index notation, we obtain:
$$
\begin{aligned}
& \int_{\Omega^0} R(\mathbf{X}, 0) Q^n_{a|i} N_a(\mathbf{X}) A^n_{b|i} N_b(\mathbf{X}) d\mathbf{X} \\
&= \int_{\partial\Omega^0} Q^n_{a|i} N_a(\mathbf{X}) T_i(\mathbf{X}, t^n) ds(\mathbf{X}) - \int_{\Omega^0} Q^n_{a|i} N_{a,j}(\mathbf{X}) P_{ij}(\mathbf{X}, t^n) d\mathbf{X}.
\end{aligned}
$$
On the left-hand side, we see that the sample values $Q^n_{a|i}$ and $A^n_{b|i}$ are in fact independent of $\mathbf{X}$, so we can move them out of the integral and obtain:
$$
\begin{aligned}
& M_{ab} Q^n_{a|i} A^n_{b|i} \\
&= \int_{\partial\Omega^0} Q^n_{a|i} N_a(\mathbf{X}) T_i(\mathbf{X}, t^n) ds(\mathbf{X}) - \int_{\Omega^0} Q^n_{a|i} N_{a,j}(\mathbf{X}) P_{ij}(\mathbf{X}, t^n) d\mathbf{X}
\end{aligned}
$$
where
$$
M_{ab} = \int_{\Omega^0} R(\mathbf{X}, 0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X}
{{numeq}}{eq:lec17:mass_matrix}
$$
is the mass matrix.

> **{{rem}}{rem:lec17:mass_matrix}[Mass Matrix Properties]**
> The mass matrix $M$ (Equation {{eqref: eq:lec17:mass_matrix}}) is symmetric and positive semi-definite because it can be expressed as:
$$
\int_{\Omega^0} BB^T d\mathbf{X},
$$
> where $B_i = \sqrt{R(\mathbf{X}, 0)} N_i(\mathbf{X})$. Thus, for any vector $z$, 
$$
z^T M z = \int_{\Omega^0} (z^T B)^2 d\mathbf{X} \geq 0.
$$
> In practice, this mass matrix may be singular. To address this, we typically use a "mass lumping" strategy to approximate the mass matrix with a diagonal and positive definite form. This is achieved by summing each row and defining:
$$
M^{\text{lump}}_{ab} = \delta_{ab} \sum_c M_{ac}.
$$

After spatial discretization, the solution of the weak form (Equation {{eqref: eq:lec17:weakform_lagrangian_tn}}) is confined to $d$ $n$-dimensional function spaces, where $n$ represents the number of sample points, assuming all interpolation functions are mutually orthogonal. This means that there could be continuous solutions to the weak form outside of our solution space. In such cases, we can only provide an approximate solution based on the chosen sample points and interpolation functions.

> **{{def}}{def:lec17:orthogonal_functions}[Orthogonal Functions]**
> Similar to the orthogonality of two vectors $\mathbf{a}$ and $\mathbf{b}$, defined as $\mathbf{a}^T \mathbf{b} = 0$, the orthogonality of two functions $f(x)$ and $g(x)$ is defined as:
$$
\int f(x) g(x) \, dx = 0.
$$
> Just as a basis of vectors can span a finite-dimensional space, orthogonal functions can form an infinite basis for a function space. Conceptually, the integral above is analogous to a vector dot product.

That being said, to generate equations solvable for the unknowns, the arbitrary test function $\mathbf{Q}$ does not need to cover all possibilities to produce an infinite number of equations. Instead, we only need to produce a finite set of equations that spans the entire solution space. Therefore, for $\hat{a}$ traversing all sample points, and $\hat{i} = 1, 2, \ldots, d$, we can assign the test function:
$$
Q_{a|i}^n = \left\{
    \begin{array}{lc}
        1, & a = \hat{a} \text{ and } i = \hat{i} \\
        0, & \text{otherwise}
    \end{array}
\right.
$$
to obtain $nd$ equations:
$$
M_{\hat{a}b} A^n_{b|\hat{i}} = \int_{\partial \Omega^0} N_{\hat{a}}(\mathbf{X}) T_{\hat{i}}(\mathbf{X}, t^n) ds(\mathbf{X}) - \int_{\Omega^0} N_{\hat{a},j}(\mathbf{X}) P_{\hat{i}j}(\mathbf{X}, t^n) d\mathbf{X},
{{numeq}}{eq:lec17:test_func_chosen}
$$
resulting in $nd$ unknowns and $nd$ equations, bringing us closer to the discrete form. 

The two integrals on the right side of Equation {{eqref: eq:lec17:test_func_chosen}} can be evaluated analytically or using quadrature rules, depending on the specific choice of interpolation functions. We will discuss these in detail in future lectures.
