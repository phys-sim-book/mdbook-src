## Fixed-Point Iteration

To obtain the solution with fully implicit friction, we can iteratively alternate between the nonlinear optimization with fixed $\lambda$, and $T$ given as
$$
\begin{align}
   & \min_{x}: E(x, \{\lambda, T\}) = \frac{1}{2} \|x - \tilde{x}^{n}\|^2_M +  \Delta t^2 \left( P_e(x) + P_b(x) + P_f(x, \{\lambda, T\}) \right) \nonumber\\ &\text{s.t.} \quad Ax = b,
    {{numeq}}{eq:lec9:discretized_IP_withBandD}
\end{align}
$$
and friction update until convergence ({{ref: alg:lec9:friction_iter}}).
<figure>
    <center>
    <b><figcaption>{{alg}}{alg:lec9:friction_iter}[Fixed-Point Iteration for Fully-Implicit Friction]</figcaption></b>
    <img src="img/lec9/alg_friction_iter.jpg" width="610">
    </center>
</figure>

If we denote
\begin{equation}
\begin{aligned}
    & f_m(\{ \lambda, T \}) = \text{arg}\min_x E(x, \{ \lambda, T\}) \\
    & f_u(x) = \text{FrictionUpdate}(x),
\end{aligned}
\end{equation}
then {{ref: alg:lec9:friction_iter}} is essentially a fixed-point iteration that finds the fixed-point of function
\begin{equation}
    (f_m \cdot f_u) (x) \equiv f_m( f_u (x)).
\end{equation}

> **{{def}}{def:lec9:fixed_point_iterations}**
> $x$ is a fixed point of function $f()$ if and only if
\begin{equation}
    x = f(x).
\end{equation}
> The fixed-point iterations find the fixed-point of a function $f()$ starting from $x^0$ by iteratively updating the estimate
\begin{equation}
    x^{i+1} \leftarrow f(x^i)
\end{equation}
> until convergence.

Since the convergence of fixed-point iterations could only be achieved given an initial guess sufficiently close to the final solution, the convergence of {{ref: alg:lec9:friction_iter}} analogously requires small time step sizes. However, note that each minimization with fixed $\{ \lambda, T \}$ ({{ref: alg:lec9:friction_iter}} line 4) is still guaranteed to converge with arbitrarily large time step sizes.

> **{{rem}}{rm:lec9:friction_visual_quality}**
> In practice, semi-implicit friction with frame-rate time step sizes can already produce results with high visual quality. For higher accuracy, running 2 to 3 fixed-point iterations for friction is generally sufficient.
