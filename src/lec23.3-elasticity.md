## Elasticity

For elasticity, similar to the 2D case, the deformation gradient $\mathbf{F}$ is also constant within each tetrahedron, and we can compute it as
$$
\begin{aligned}
    \mathbf{F} = & \frac{\partial \mathbf{x}}{\partial (\beta, \gamma, \tau)} (\frac{\partial \mathbf{X}}{\partial (\beta, \gamma, \tau)})^{-1} 
    \\
    \approx & \frac{\partial \hat{\mathbf{x}}}{\partial (\beta, \gamma, \tau)} (\frac{\partial \mathbf{X}}{\partial (\beta, \gamma, \tau)})^{-1} \\
    =& [\mathbf{x}_2 - \mathbf{x}_1, \mathbf{x}_3 - \mathbf{x}_1, \mathbf{x}_4 - \mathbf{x}_1] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1, \mathbf{X}_4 - \mathbf{X}_1]^{-1}.
\end{aligned}
$$
For force and Hessian computation, the required $\partial \mathbf{F} / \partial \mathbf{x}$ can be computed using
$$
\begin{aligned}
    \nabla^\mathbf{X} N_1(\mathbf{X}) & = \frac{\partial (1 - \beta - \gamma - \tau)}{\partial \mathbf{X}} = (\frac{\partial (1 - \beta - \gamma - \tau)}{\partial (\beta, \gamma, \tau)} (\frac{\partial \mathbf{X}}{\partial (\beta, \gamma, \tau)})^{-1})^T \\
    & = ([-1, -1, -1] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1, \mathbf{X}_4 - \mathbf{X}_1]^{-1})^T
\end{aligned}
$$
and similarly
$$
    \begin{aligned}
        \nabla^\mathbf{X} N_2(\mathbf{X}) & = \frac{\partial \beta}{\partial \mathbf{X}} = ([1, 0, 0] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1, \mathbf{X}_4 - \mathbf{X}_1]^{-1})^T,
        \\
        \nabla^\mathbf{X} N_3(\mathbf{X}) & = \frac{\partial \gamma}{\partial \mathbf{X}} = ([0, 1, 0] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1, \mathbf{X}_4 - \mathbf{X}_1]^{-1})^T,
        \\
        \nabla^\mathbf{X} N_3(\mathbf{X}) & = \frac{\partial \tau}{\partial \mathbf{X}} = ([0, 0, 1] [\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1, \mathbf{X}_4 - \mathbf{X}_1]^{-1})^T.
    \end{aligned}
$$
With $\mathbf{F}$, the computation of strain energy $\Psi$, stress $\mathbf{P}$ and stress derivative $\partial \mathbf{P}/\partial \mathbf{F}$ can all be found in [Strain Energy](./lec13-strain_energy.md) and [Stress and Its Derivatives](./lec14-stress_and_derivatives.md), and the computation of forces and Hessian matrices follow the same spirit as in 2D.

To guarantee non-inversion of the tetrahedral elements during the simulation, the critical step size $\alpha^{I}$ that first brings the volume of any tetrahedra to $0$ can be obtained by solving a 1D equation per tetrahedron
$$
    V({\mathbf{x}_i + \alpha^I \mathbf{p}_i}) = 0,
$$
and then take the minimum of the solved step sizes.
Here $\mathbf{p}_i$ is the search direction of node $i$, and in 3D, this is equivalent to
$$
    \det([\mathbf{x}^\alpha_{21}, \mathbf{x}^\alpha_{31}, \mathbf{x}^\alpha_{41}]) \equiv (\mathbf{x}^\alpha_{21} \times \mathbf{x}^\alpha_{31}) \cdot \mathbf{x}^\alpha_{41} = 0
    {{numeq}}{eq:lec23:det_tri_equals_0}
$$
with $\mathbf{x}^\alpha_{ij} = \mathbf{x}_{ij} + \alpha^I \mathbf{p}_{ij}$ and $\mathbf{x}_{ij} = \mathbf{x}_i - \mathbf{x}_j$, $\mathbf{p}_{ij} = \mathbf{p}_i - \mathbf{p}_j$. Expanding Equation {{eqref: eq:lec23:det_tri_equals_0}}, we obtain the following cubic equation for $\alpha^I$:

$$
\begin{aligned}
\big( (\mathbf{p}_{21} \times \mathbf{p}_{31}) \cdot \mathbf{p}_{41} \big) {\alpha^I}^3  \\+ \big( (\mathbf{x}_{21} \times \mathbf{p}_{31} + \mathbf{p}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{p}_{41} + (\mathbf{p}_{21} \times \mathbf{p}_{31}) \cdot \mathbf{x}_{41} \big) {\alpha^I}^2 \\
+ \big( (\mathbf{x}_{21} \times \mathbf{p}_{31} + \mathbf{p}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{x}_{41} + (\mathbf{x}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{p}_{41} \big) \alpha^I \\ + (\mathbf{x}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{x}_{41} = 0,
\end{aligned}
$$

This cubic equation can sometimes degenerate into a quadratic or linear equation, particularly when node movements do not substantially alter the tetrahedron's volume. To address potential numerical instability, we scale the equation terms based on the constant term coefficient:

$$
\begin{aligned}
&\frac{ (\mathbf{p}_{21} \times \mathbf{p}_{31}) \cdot \mathbf{p}_{41} }{(\mathbf{x}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{x}_{41}} {\alpha^I}^3 \\ &+ \frac{ (\mathbf{x}_{21} \times \mathbf{p}_{31} + \mathbf{p}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{p}_{41} + (\mathbf{p}_{21} \times \mathbf{p}_{31}) \cdot \mathbf{x}_{41} }{(\mathbf{x}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{x}_{41}} {\alpha^I}^2 \\
&+ \frac{ (\mathbf{x}_{21} \times \mathbf{p}_{31} + \mathbf{p}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{x}_{41} +(\mathbf{x}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{p}_{41} }{(\mathbf{x}_{21} \times \mathbf{x}_{31}) \cdot \mathbf{x}_{41}} \alpha^I + 1 = 0,
\end{aligned}
{{numeq}}{eq:lec23:scaled_quad_eq_of_alphaI}
$$

ensuring that magnitude checks can be safely performed with standard thresholds (e.g., $10^{-6}$).

Practically, we also ensure some safety margin by solving for $\alpha^I$ that reduces the volume of any tetrahedron by 80%, modifying the constant term coefficient in Equation {{eqref: eq:lec23:scaled_quad_eq_of_alphaI}} from $1$ to $0.8$. If no positive real roots are found, the step size can be considered safe, and inversion will not occur. Here is the C++ code snippet for solving this scaled cubic equation:

{{imp}}{imp:lec23:cubic_solve}[Cubic Equation Solver]
```c++
double getSmallestPositiveRealRoot_cubic(double a, double b, double c, double d,
    double tol)
{
    // return negative value if no positive real root is found
    double t = -1;

    if (abs(a) <= tol)
        t = getSmallestPositiveRealRoot_quad(b, c, d, tol); // covered in the 2D case
    else {
        complex<double> i(0, 1);
        complex<double> delta0(b * b - 3 * a * c, 0);
        complex<double> delta1(2 * b * b * b - 9 * a * b * c + 27 * a * a * d, 0);
        complex<double> C = pow((delta1 + sqrt(delta1 * delta1 - 4.0 * delta0 * delta0 * delta0)) / 2.0, 1.0 / 3.0);
        if (std::abs(C) == 0.0) // a corner case
            C = pow((delta1 - sqrt(delta1 * delta1 - 4.0 * delta0 * delta0 * delta0)) / 2.0, 1.0 / 3.0);

        complex<double> u2 = (-1.0 + sqrt(3.0) * i) / 2.0;
        complex<double> u3 = (-1.0 - sqrt(3.0) * i) / 2.0;

        complex<double> t1 = (b + C + delta0 / C) / (-3.0 * a);
        complex<double> t2 = (b + u2 * C + delta0 / (u2 * C)) / (-3.0 * a);
        complex<double> t3 = (b + u3 * C + delta0 / (u3 * C)) / (-3.0 * a);

        if ((abs(imag(t1)) < tol) && (real(t1) > 0))
            t = real(t1);
        if ((abs(imag(t2)) < tol) && (real(t2) > 0) && ((real(t2) < t) || (t < 0)))
            t = real(t2);
        if ((abs(imag(t3)) < tol) && (real(t3) > 0) && ((real(t3) < t) || (t < 0)))
            t = real(t3);
    }
    return t;
}
```