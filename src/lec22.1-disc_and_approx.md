## Discretization and Approximation

From Equation {{eqref: eq:lec18:smooth-frict}}, the friction force per unit area is defined as

$$
\mathbf{T}_F(\mathbf{X}, t) = - \mu \|\mathbf{T}_C(\mathbf{X}, t)\| \ f(\|\mathbf{V}_F(\mathbf{X}, t)\|) \ \mathbf{s}(\mathbf{V}_F(\mathbf{X}, t)),
$$

where $\mu$ is the friction coefficient, $\mathbf{T}_C$ is the normal contact force, and $\mathbf{V}_F$ is the relative sliding velocity. Here $\mathbf{s}(\mathbf{V}_F) = \frac{\mathbf{V}_F}{\|\mathbf{V}_F\|}$ when $\|\mathbf{V}_F\| > 0$, while $\mathbf{s}(\mathbf{V}_F)$ takes any unit vector orthogonal to the normal $\mathbf{N}(\mathbf{X}, t)$ when $\|\mathbf{V}_F\| = 0$. Additionally, the friction scaling function $f$ is also nonsmooth with respect to $\mathbf{V}_F$, as $f(\|\mathbf{V}_F\|) = 1$ when $\|\mathbf{V}_F\| > 0$, and $f(\|\mathbf{V}_F\|) \in [0, 1]$ when $\|\mathbf{V}_F\| = 0$.

It is important to note that without temporal discretization, there is no potential energy for friction. However, similar to [Frictional Contact](./lec9-friction.md), once we discretize the normal force magnitude and the tangent operator to the last time step and smoothly approximate the friction scaling function $f$, the friction force at the $(n+1)$-th time step becomes integrable with respect to $\mathbf{x}$, and we obtain

$$
\mathbf{T}^{n+1}_F(\mathbf{X}) \approx -\frac{\partial D^{n+1}(\mathbf{X})}{\partial \mathbf{x}^{n+1}(\mathbf{X})} = -\frac{\partial \big( \mu \|\mathbf{T}^{n}_C(\mathbf{X})\| f_0(\| \bar{\mathbf{V}}^{n+1}_F(\mathbf{X}) \hat{h} \|)  \big)}{\partial \mathbf{x}^{n+1}(\mathbf{X})}.
$$

Here, $\bar{\mathbf{V}}^{n+1}_F(\mathbf{X}) = (\mathbf{I} - \mathbf{N}^{n}(\mathbf{X}) \mathbf{N}^{n}(\mathbf{X})^T) (\mathbf{V}^{n+1}(\mathbf{X}) - \mathbf{V}^{n+1}(\mathbf{X}_2))$ is the approximate relative sliding velocity, where $\mathbf{N}^{n}$ and $\mathbf{X}_2$ are the normal direction and the point in contact with $\mathbf{X}$ in the last time step, $\hat{h}I = (\partial v/\partial x)^{-1}$, and 

$$
f_0(y) =
\begin{cases}
    -\frac{y^3}{3\epsilon_v^2 \hat{h}^2} + \frac{y^2}{\epsilon_v \hat{h}} + \frac{\epsilon_v  \hat{h}}{3}, & y \in[0, \epsilon_v \hat{h}); \\
    y, & y \geq  \epsilon_v \hat{h}.
\end{cases}
$$

Therefore, considering self-contact, the approximate friction potential over the entire boundary can be written as

$$
\int_{\Gamma_C} \frac{1}{2}  \mu \|\mathbf{T}^{n}_C(\mathbf{X})\| f_0(\| \bar{\mathbf{V}}^{n+1}_F(\mathbf{X}) \hat{h} \|) \, ds(\mathbf{X}),
$$

where the $\frac{1}{2}$ scaling comes from double counting the friction between each pair of contact points in the integral (similar to the normal contact forces in [Boundary Conditions and Frictional Contact](./lec18-BC_and_fric.md)).

After discretizing the boundary curves as polylines and approximating the max operator in the normal contact force component using summations ([Piecewise Linear Boundaries](./lec20-pw_linear_boundary.md)), we similarly obtain the spatially discretized friction potential:

$$
\int_{\Gamma_C} \sum_{e \in \mathcal{E} - I(\mathbf{X})} \frac{1}{2} \mu \big(-\frac{\partial b( d^\text{PE}(\mathbf{x}^{n}(\mathbf{X}), e), \hat{d})}{\partial d}\big) f_0(\| \bar{\mathbf{V}}^{n+1}_F(\mathbf{X}, e) \hat{h} \|) \, ds(\mathbf{X}).
$$

Here, $d^\text{PE}(\mathbf{x}^{n}(\mathbf{X}), e)$ is the point-edge distance between $\mathbf{x}^{n}(\mathbf{X})$ and edge $e$ in the last time step, and $\bar{\mathbf{V}}^{n+1}_F(\mathbf{X}, e)$ is the approximate relative sliding velocity of the point-edge pair with contact normal and the closest point discretized to the last time step (see next section for details).

If we choose boundary nodes as quadrature points to approximate the integral, we finally obtain our discrete friction potential:

$$
\begin{aligned}
    P_f(x) 
    & = \sum_{\hat{a}} A_{\hat{a}} \sum_{e \in \mathcal{E} - I(\mathbf{X}_{\hat{a}})} \frac{1}{2}\mu \big(-\frac{\partial b( d^\text{PE}(\mathbf{x}^{n}_{\hat{a}}, e), \hat{d})}{\partial d}\big) f_0(\| \bar{\mathbf{V}}^{n+1}_F(\mathbf{X}_{\hat{a}}, e) \hat{h} \|) \\
    & = \sum_{k \in \{ (\hat{a}, e) \} } \mu \lambda_k^{n} f_0(\|\bar{\mathbf{v}}_k \hat{h}\|)
\end{aligned}
$$

where $A_{\hat{a}} = \frac{\|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} - 1} \| + \|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} + 1} \|}{2}$ is the integration weight. Denoting $\bar{\mathbf{v}}_k = \bar{\mathbf{V}}^{n+1}_F(\mathbf{X}_{\hat{a}}, e)$ and $\lambda^{n}_k = \frac{1}{2}A_{\hat{a}} \left(-\frac{\partial b( d^\text{PE}(\mathbf{x}^{n}_{\hat{a}}, e), \hat{d})}{\partial d}\right)$, the expression of $P_f$ agrees with the discrete form of Equation {{eqref: eq:lec9:fric_potential}} we directly derived, except that here $k$ traverses all non-incident point-edge pairs on the boundary.

Based on this discrete form of the smoothed semi-implicit friction potential, we now need to determine how to calculate $\lambda$ and $\bar{\mathbf{v}}$ for point-edge pairs, implement the computation of the value, gradient, and Hessian of $P_f(x)$, and then incorporate them into the optimization.
