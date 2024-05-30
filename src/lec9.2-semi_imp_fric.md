## Semi-Implicit Discretization

However, challenges still remain on incorporating friction into the optimization time integration. A major problem is that friction is not a conservative force and there is no well-defined potential such that taking the opposite of its gradient produces the frictional force. In other words, implicit friction force is not integrable. Without a potential energy, backtracking line search could not be performed, and thus guarantees on the stability and convergence of the optimization will be broken.

In fact, whether a force has well-defined potential energy really depends on the temporal discretization. For example, with explicit time integration, any force $f$ is constant within a time step and it has a potential energy $-f^T x$.
Taking this inspiration, we could make friction force integrable with a smarter temporal discretization. Making friction force constant within a time step would certainly restrict the size of the time step to obtain high quality results. Therefore, we discretize part of the friction force explicitly and formulate an integrable semi-implicit friction force.

Following IPC, we fix the normal force magnitude $\lambda$ (the ones only used in calculating friction) and the tangent operator $T$ during the nonlinear optimization to the value in the last time step $n$: $\lambda^n = \lambda(x^n)$, and $T^n=T(x^n)$, which then makes the friction force integrable with a potential energy
$$
    P_f(x) = \sum_{k} \mu \lambda_k^n f_0(\|\bar{\mathbf{v}}_k \hat{h}\|),
    {{numeq}}{eq:lec9:fric_potential}
$$
where $\bar{\mathbf{v}}_k = (T_k^n)^T v$, $\hat{h}I = (\partial v/\partial x)^{-1}$, and 
$$
    f_0(y) =
    \begin{cases}
        -\frac{y^3}{3\epsilon_v^2 \hat{h}^2} + \frac{y^2}{\epsilon_v \hat{h}} + \frac{\epsilon_v  \hat{h}}{3}, & y \in[0, \epsilon_v \hat{h})\\
     	y, & y \geq  \epsilon_v \hat{h},
    \end{cases}
    {{numeq}}{eq:lec9:f0}
$$
so that $f'_0(y)=f_1(y/\hat{h})$. Here $\hat{h}$ is a constant multiple of the time step size $h$ for most linear (multi-)step time integration methods including implicit Euler and higher-order backward difference formulas, etc.
Then, taking the gradient of Equation {{eqref: eq:lec9:fric_potential}} w.r.t. $x$ we obtain
$$
    -\nabla P_f(x) = -\sum_{k}\mu \lambda^n_k T^n_k f_1(\|\bar{\mathbf{v}}_k\|) \mathbf{s}(\bar{\mathbf{v}}_k),
    {{numeq}}{eq:lec9:fric_grad}
$$
which is a semi-implicit discretization of our mollified friction force with explicit terms $\lambda^n_k$ and $T^n_k$.
The Hessian of $P_f$ can be calculated as
$$
\begin{aligned}
    &\nabla^2 P_f(x) \nonumber \\
    =& \sum_{k}\mu \lambda^n_k T^n_k \Big(\frac{f'_1(\|\bar{\mathbf{v}}_k\|)\|\bar{\mathbf{v}}_k\| - f_1(\|\bar{\mathbf{v}}_k\|)}{\|\bar{\mathbf{v}}_k\|^3} \bar{\mathbf{v}}_k \bar{\mathbf{v}}_k^T
     + \frac{f_1(\|\bar{\mathbf{v}}_k\|)}{\|\bar{\mathbf{v}}_k\|}\mathbf{I}_{3}\Big) {T_k^n}^T \frac{\partial v}{\partial x}.
\end{aligned}
     {{numeq}}{eq:lec9:fric_hess}
$$

> **{{rem}}{rm:lec9:fric_grad_div0}**
> In the friction gradient and Hessian expression (Equation {{eqref: eq:lec9:fric_grad}} and Equation {{eqref: eq:lec9:fric_hess}}), there are $\|\mathbf{v}_k\|$ in the denominators, which could be $0$ when there is no relative sliding motion at a contact point.
> To avoid division by $0$ during the computation, for friction gradient, we can derive
$$
     \frac{f_1(\|\bar{\mathbf{v}}_k\|)}{\|\bar{\mathbf{v}}_k\|} =
     \begin{cases}
     	-\frac{\|\bar{\mathbf{v}}_k\|}{\epsilon_v^2 } + \frac{2}{\epsilon_v}, & \|\bar{\mathbf{v}}_k\| \in[0, \epsilon_v)\\
     	1/\|\bar{\mathbf{v}}_k\|, & \|\bar{\mathbf{v}}_k\| \geq  \epsilon_v,
  \end{cases}
  {{numeq}}{eq:lec9:f1_term}
 $$
> which is well-defined everywhere, and so we obtain
 $$
     -\nabla P_{f,k}(x) = -\mu \lambda^n_k T^n_k \frac{f_1(\|\bar{\mathbf{v}}_k\|)}{\| \bar{\mathbf{v}}_k \|} \bar{\mathbf{v}}_k = 0 \quad \text{when} \quad \|\bar{\mathbf{v}}_k\| = 0.
 $$
> For friction Hessian, we can derive
 $$
     \frac{f'_1(\|\bar{\mathbf{v}}_k\|)\|\bar{\mathbf{v}}_k\| - f_1(\|\bar{\mathbf{v}}_k\|)}{\|\bar{\mathbf{v}}_k\|^2} =
     \begin{cases}
     	-1/\epsilon_v^2, & \|\bar{\mathbf{v}}_k\| \in[0, \epsilon_v)\\
     	-1/\|\bar{\mathbf{v}}_k\|^2, & \|\bar{\mathbf{v}}_k\| \geq  \epsilon_v,
  \end{cases}
  {{numeq}}{eq:lec9:hess_term}
 $$
> which is also well-defined everywhere, and since $\bar{\mathbf{v}}_k \bar{\mathbf{v}}_k^T /\|\bar{\mathbf{v}}_k\| = \bm{0}$ when $\|\bar{\mathbf{v}}_k\| = 0$, we know that
 $$
     \nabla^2 P_{f,k}(x) = \mu \lambda^n_k T^n_k \Big(\frac{f_1(\|\bar{\mathbf{v}}_k\|)}{\|\bar{\mathbf{v}}_k\|}\mathbf{I}_{3}\Big) {T_k^n}^T \frac{\partial v}{\partial x} \quad \text{when} \quad \|\bar{\mathbf{v}}_k\| = 0.
$$

> **{{rem}}{rm:lec9:friction_variations}**
> The friction formulation in this lecture is introduced slightly differently from the original IPC @@li2020incremental in 2 places:
> 1.  We directly use the relative sliding velocity $\mathbf{v}_k$ rather than the relative sliding displacement $\mathbf{u}_k = \hat{h} \mathbf{v}_k$ in IPC as the input to the mollifier $f_1()$, and so our $f_1()$ differs from that in the IPC on $\hat{h}$ in the denominators. When time integration rules other than implicit Euler is applied (so $x^{n+1}-x^n \neq \hat{h} v^{n+1}$), calling $\mathbf{u}_k$ the relative sliding displacement is inappropriate and may cause confusions.
> 2.  We did not introduce a tangent basis to express relative sliding velocity in the tangent space, because this is not necessary in computing the friction energy, gradient, and Hessian.
