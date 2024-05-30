## Smooth Dynamic-Static Transition

To model frictional contact, local frictional forces $F_k$ can be added for every active contact point pair $k$.
For each such pair $k$, at the current state $\{x, v\}$, a consistently oriented sliding basis $T_k(x) \in \mathbb{R}^{d m\times d}$ can be constructed, where $m$ is the total number of simulated nodes and $d$ is the dimension of space, such that $\mathbf{v}_k = T_k(x)^T v \in \mathbb{R}^{d}$ provides the local relative sliding velocity that is orthogonal to the distance gradient in the normal direction $\mathbf{n}_k(x)$.

> **{{exp}}{exp:lec9:particle_sphere_contact}[Particle Sliding on Sphere]**
> For a particle with velocity $\mathbf{v}_p \in \mathbb{R}^3$ moving on the surface of a sphere with velocity $\mathbf{v}_s \in \mathbb{R}^3$ (no rotation), the relative sliding velocity $\mathbf{v}_k$ here can be calculated as 
$$
    \mathbf{v}_k = (\mathbf{v}_p - \mathbf{v}_s) - \mathbf{n}_k \cdot (\mathbf{v}_p - \mathbf{v}_s) \mathbf{n}_k = (\mathbf{I}_3 - \mathbf{n}_k \mathbf{n}_k^T) (\mathbf{v}_p - \mathbf{v}_s).
$$
> If we stack the velocity of the particle and the sphere for this system to obtain $v = [\mathbf{v}_p^T, \mathbf{v}_s^T]^T \in \mathbb{R}^6$, we now know that $T_k$ is simply
$$
    T_k(x) = 
    \begin{bmatrix}
        \mathbf{I}_3 - \mathbf{n}_k(x) \mathbf{n}_k(x)^T \\
        \mathbf{n}_k(x) \mathbf{n}_k(x)^T - \mathbf{I}_3
    \end{bmatrix} \in \mathbb{R}^{6 \times 3}.
$$
> For more general cases like mesh-mesh contact, the form of $T_k$ only varies in how the relative velocity at the contact point pair $k$ is related to the velocity at the simulated nodes.

Maximizing dissipation rate subject to the Coulomb constraint defines friction forces variationally
$$
    F_k(x) = T_k(x) \ \text{arg}\min_{\boldsymbol{\beta} \in \mathbb{R}^{d}} \boldsymbol{\beta}^T \mathbf{v}_k \quad \text{s.t.} \quad  \|\boldsymbol{\beta}\| \leq \mu \lambda_k \ \ \text{and} \ \ \boldsymbol{\beta} \cdot \mathbf{n}_k = 0,
    {{numeq}}{eq:frictionForceDef}
$$
where $\lambda_k = -w_k\frac{\partial b}{\partial d_k}$ is the contact force magnitude 
and $\mu$ is the local friction coefficient. This is equivalent to
$$
    F_k(x) = - \mu \lambda_k T_k(x) f(\|\mathbf{v}_k\|) \mathbf{s}(\mathbf{v}_k),
    {{numeq}}{eqref: eq:smooth-frict}
$$
with $\mathbf{s}(\mathbf{v}_k) = \frac{\mathbf{v}_k}{\|\mathbf{v}_k\|}$ when $\|\mathbf{v}_k\|> 0$, while $\mathbf{s}(\mathbf{v}_k)$ takes any unit vector orthogonal to $\mathbf{n}_k$ when $\|\mathbf{v}_k\| = 0$. In addition, the friction scaling function, $f$, is also nonsmooth with respect to $\mathbf{v}_k$ since $f(\|\mathbf{v}_k\|) = 1$ when $\|\mathbf{v}_k\|> 0$, and $f(\|\mathbf{v}_k\|) \in [0,1]$ when $\|\mathbf{v}_k\| = 0$. These non-smoothness would severely slow down and even break convergence of gradient-based optimization.

<figure>
    <center>
    <img src="img/lec9/friction_illustration.jpg" width="150">
    </center>
    <figcaption><b>{{fig}}{fig:lec9:friction_illustration}</b> An illustration of $T_k$, $\mathbf{v}_k$, $\mathbf{n}_k$, and $F_k$ when a point slides on a sphere. </figcaption>
</figure>

> **{{rem}}{rm:lec9:contact_force_magnitude}[Contact Force Magnitude]**
> $\lambda_k = -w_k\frac{\partial b}{\partial d_k}$ is the contact force magnitude because at node $k$, the contact force is $-w_k \nabla_{\mathbf{x}_k} b(d_k(x)) = -w_k\frac{\partial b}{\partial d_k} \nabla_{\mathbf{x}_k} d_k(x)$. Therefore, $\lambda_k = \| -w_k \frac{\partial b}{\partial d_k} \nabla_{\mathbf{x}_k} d_k(x) \| = -w_k\frac{\partial b}{\partial d_k}$ since $\frac{\partial b}{\partial d_k} < 0$ and $\|\nabla_{\mathbf{x}_k} d_k(x)\| = 1$.

To enable efficient and stable optimization, the friction-velocity relation in the transition to static friction can be mollified by replacing $f$ with a smoothly approximated function. Following IPC, we use
$$
\begin{align}
f_1(y) =
\begin{cases}
 	-\frac{y^2}{\epsilon_v^2 } + \frac{2y}{\epsilon_v}, & y \in[0, \epsilon_v)\\
 	1, & y \geq  \epsilon_v,
 \end{cases}
 {{numeq}}{eqref: eq:fric_mollifier}
\end{align}
$$
where $f_1'(\epsilon_v) = 0$ and a velocity magnitude bound $\epsilon_v$ (in units of $m/s$) below which sliding velocities $\mathbf{v}_k$ are treated as static is defined for bounded approximation error ({{ref: fig:lec9:friction_plot}}).

<figure>
    <center>
    <img src="img/lec9/friction_clamping.jpg" width="320">
    </center>
    <figcaption><b>{{fig}}{fig:lec9:friction_plot}</b> A 1D illustration of the smoothed relation between friction force and sliding velocity. Decreasing $\epsilon_v$ asymptotically matches the discontinuous Coulomb friction model. </figcaption>
</figure>
