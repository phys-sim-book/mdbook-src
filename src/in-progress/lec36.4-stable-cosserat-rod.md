## Stable Cosserat Rods

### Limitations of Traditional Solvers and a New Approach

The variational energy minimization from {{eqref:eq:time_integration:variational_energy}} is challenging due to tight coupling between positions $\mathbf{x}$ and orientations $\mathbf{q}$, combined with non-linear unit-quaternion constraints on every segment.

Traditional methods struggle with this problem. Local iterative solvers like **XPBD** suffer from slow convergence, requiring extremely small time steps for stiff rods. Global implicit methods are more robust but computationally expensive, making them unsuitable for interactive applications.

This trade-off motivates the **Stable Cosserat Rods** method {{#cite hsu2025stable}}, which introduces a novel solver that is highly stable with large time steps, efficient for interactive simulations, and inherently parallelizable.

-----

### The Quasi-Static Split: Decoupling Position and Orientation

> **Core Insight**: For a thin rod, the contribution of angular momentum to the dynamics is often negligible, allowing us to make a quasi-static assumption for the rod's orientation that decouples the optimization problem.

But what does this mean mathematically?

#### The Vanishing Moment of Inertia

For a thin rod ($r \ll L$), the moment of inertia tensor simplifies significantly: $J_{xx}, J_{yy} \approx \frac{1}{12}mL^2$ while $J_{zz} = \frac{1}{2}mr^2 \to 0$. This means the rod's inertial resistance to twisting about its own axis vanishes, making any non-zero twisting torque produce almost infinite angular acceleration—a recipe for numerical instability.

> **Physical Insight**: For thin rods, the vanishing twist inertia means orientations instantaneously adjust to balance torques, enabling a quasi-static assumption that decouples the optimization problem.

Physically, this means the rod's orientation does not have "rotational momentum" that carries it forward. Instead, it instantaneously adjusts to balance any applied torques. This is the **quasi-static** (or "almost static") state: at any instant, the orientations are assumed to be in equilibrium.

#### Splitting the Optimization

This quasi-static assumption allows us to remove the rotational inertia term from the variational energy minimization {{eqref:eq:time_integration:variational_energy}}:

$$
{{numeq}}{eq:quasi_static:inertia_term}
\frac{1}{2h^2}\|\mathbf{q}-\mathbf{w}\|_J^2 \to 0
$$

With this term gone, the problem beautifully decouples. Instead of solving one large, complex optimization, we can solve two simpler sub-problems in an alternating fashion:

1.  **Position Update:** With orientations $\mathbf{q}$ held constant, solve for the new positions $\mathbf{x}$ using the discrete energy {{eqref:eq:discrete:total_energy_scalar}}:

$$
{{numeq}}{eq:quasi_static:position_update}
\mathbf{x} = \arg\min_{\mathbf{x}} \frac{1}{2h^2}\|\mathbf{x}-\mathbf{y}\|_M^2 + E^{total}(\mathbf{x},\mathbf{q}_{fixed})
$$

2.  **Orientation Update:** With positions $\mathbf{x}$ held constant, solve for the new orientations $\mathbf{q}$ by minimizing *only* the potential energy {{eqref:eq:discrete:total_energy_scalar}}, subject to the unit-quaternion constraint:

    $$
{{numeq}}{eq:quasi_static:orientation_update}
\mathbf{q} = \arg\min_{\mathbf{q}} E^{total}(\mathbf{x}_{fixed},\mathbf{q}) \quad \text{s.t.} \quad \|\mathbf{q}_i\| = 1 \quad \forall i
    $$

This split approach is the foundation of the method's stability and efficiency. The remaining challenge is how to solve the orientation sub-problem quickly.

-----

### A Closed-Form, Local Orientation Solver

Solving the orientation sub-problem globally is still expensive. The Stable Cosserat Rods method instead uses a **Gauss-Seidel-style local relaxation** {{#cite hsu2025stable}}. The algorithm iterates over each segment's orientation $\mathbf{q}_i$ one at a time, finding its optimal value while treating its neighbors as fixed.

The optimality condition for a single $\mathbf{q}_i$ is that the net torque on it must be zero. Introducing a Lagrange multiplier $\lambda$ to enforce the unit-norm constraint, $\|\mathbf{q}_i\|=1$, gives the equilibrium condition:

$$
{{numeq}}{eq:orientation:equilibrium}
\boldsymbol{\tau}_i^{net} - \lambda \mathbf{q}_i = 0
$$

The net torque $\boldsymbol{\tau}_i^{net} = \boldsymbol{\tau}_{i}^{ss} + \boldsymbol{\tau}_{i-1,i}^{bt} + \boldsymbol{\tau}_{i,i}^{bt}$ is the sum of torques acting on $\mathbf{q}_i$. These are derived as the negative gradients of the potential energies from {{eqref:eq:discrete:total_energy_scalar}}:

* **Stretch Torque**: $\boldsymbol{\tau}_{i}^{ss} = -2k_i^{ss}\left(\frac{\mathbf{x}_{i+1}-\mathbf{x}_i}{l_i} - \mathbf{d}_{i,3}\right) \mathbf{q}_i\mathbf{e}_3$
* **Bend/Twist Torques**: $\boldsymbol{\tau}_{i,i}^{bt} = -k_i^{bt}(\mathbf{q}_i - \phi_i \mathbf{q}_{i+1}\overline{\mathbf{q}}_i^0)$ and $\boldsymbol{\tau}_{i-1,i}^{bt} = -k_{i-1}^{bt}(\mathbf{q}_i - \phi_{i-1}\mathbf{q}_{i-1}\mathbf{q}_{i-1}^0)$

> **Important Note**: Unlike the standard discretization approach that uses only the imaginary part $\mathfrak{I}(\cdot)$ of the quaternion product, the Stable Cosserat Rods method uses the **full quaternion product** {{#cite hsu2023sag}}. This means our Darboux vector becomes:
$$\boldsymbol{\Omega}_i = \frac{2}{l} \mathbf{q}_{i-1}\overline{\mathbf{q}}_i$$
and the bend/twist strain becomes:
$$\mathbf{C}_i^{bt} = \mathbf{q}_{i-1}\overline{\mathbf{q}}_i - \phi_i \mathbf{q}_{i-1}^0\overline{\mathbf{q}}_i^0$$
This full quaternion formulation eliminates the ambiguity present in the standard approach and leads to more stable behavior in the solver. The $\phi_i$ terms handle quaternion ambiguity (since $q$ and $-q$ represent the same rotation) by choosing the closest quaternionic pole for numerical stability.

#### Deriving a Compact Equilibrium Equation

To simplify, we can group the terms. As pointed out by Hsu et al., any torque acting along $\mathbf{q}_i$ will be canceled out by the unit quaternion constraint. These can then be substituted into the static-equilibrium condition and simplified to obtain:

$$-2k^{ss}_i l^{-1}_i (\mathbf{x}_{i+1} - \mathbf{x}_i)\mathbf{q}_i \mathbf{e}_3 + k^{bt}_{i-1}\phi_{i-1}\mathbf{q}_{i-1}\mathbf{q}^0_{i-1} + k^{bt}_i \phi_i \mathbf{q}_{i+1}\mathbf{q}^0_i - \lambda\mathbf{q}_i = 0$$

As pointed out by Hsu et al., any torque acting along $\mathbf{q}_i$ will be canceled out by the unit quaternion constraint. This allows us to simplify the terms and group them into the compact form. This leads to the compact form of the equilibrium equation:

$$
{{numeq}}{eq:orientation:compact_equilibrium}
\mathbf{v}\mathbf{q}_i\mathbf{e}_3 + \mathbf{b} - \lambda \mathbf{q}_i = 0
$$

Here, we have conveniently grouped all stretch-related influences into a pure quaternion vector $\mathbf{v}$ and all bend/twist influences into a vector $\mathbf{b}$:

$$\mathbf{v} = -2k_{i}^{ss}l_{i}^{-1}(\mathbf{x}_{i+1}-\mathbf{x}_{i})$$

$$\mathbf{b} = \underbrace{k_{i-1}^{bt}\phi_{i-1}\mathbf{q}_{i-1}\mathbf{q}_{i-1}^{0}}_{\text{Bending torque from left neighbor}} + \underbrace{k_{i}^{bt}\phi_{i}\mathbf{q}_{i+1}\overline{\mathbf{q}}_{i}^{0}}_{\text{Bending torque from right neighbor}}$$

This grouping is elegant because it allows for an arbitrary number of connections; $\mathbf{b}$ simply becomes the sum of all bending constraint contributions at that junction.

#### Generalizing to Complex Structures

The vector $\mathbf{b}$ represents the total torque acting on a segment's orientation $\mathbf{q}_i$ due to bending and twisting. For a simple linear rod, each segment is connected to two neighbors:

$$\mathbf{b} = k_{i-1}^{bt}\phi_{i-1}\mathbf{q}_{i-1}\mathbf{q}_{i-1}^{0} + k_{i}^{bt}\phi_{i}\mathbf{q}_{i+1}\overline{\mathbf{q}}_{i}^{0}$$

For complex branching structures (like tree branches or knit fabrics), this generalizes to a summation over all connected segments:

$$\mathbf{b}_i = \sum_{C_{i}^{bt}}
\begin{cases}
k_{ji}^{bt}\phi_{ji}\mathbf{q}_{j}\mathbf{q}_{ji}^{0} & \text{for } C_{ji}^{bt} \\
k_{ij}^{bt}\phi_{ij}\mathbf{q}_{j}\overline{\mathbf{q}}_{ij}^{0} & \text{for } C_{ij}^{bt}
\end{cases}$$

This formulation allows the same local update rule to naturally handle complex, branching structures without any changes to the core solver, making it incredibly versatile.

#### Closed-Form Solution for Orientation $\mathbf{q}_i$

The compact equilibrium equation $\mathbf{v}\mathbf{q}_i\mathbf{e}_3 + \mathbf{b} - \lambda \mathbf{q}_i = 0$ is a quaternion linear equation. We can solve for $\mathbf{q}_i$ by rearranging and applying algebraic manipulation:

$$\lambda \mathbf{q}_i - \mathbf{v}\mathbf{q}_i\mathbf{e}_3 = \mathbf{b}$$

Left-multiplying by $(\lambda + \mathbf{v}(\cdot)\mathbf{e}_3)$ and expanding, the middle terms cancel. Since $\mathbf{v}^2 = -|\mathbf{v}|^2$ and $\mathbf{e}_3^2 = -1$, we obtain:

$$(\lambda^2 - |\mathbf{v}|^2)\mathbf{q}_i = \lambda\mathbf{b} + \mathbf{v}\mathbf{b}\mathbf{e}_3$$

This gives us a closed-form solution for $\mathbf{q}_i$ in terms of the unknown Lagrange multiplier $\lambda$:

$$
{{numeq}}{eq:orientation:closed_form_solution}
\mathbf{q}_i(\lambda) = \frac{\mathbf{v}\mathbf{b}\mathbf{e}_3 + \lambda\mathbf{b}}{\lambda^2 - |\mathbf{v}|^2}
$$

#### Approximating the Lagrange Multiplier $\lambda$

To enforce the unit-norm constraint $\|\mathbf{q}_i(\lambda)\|=1$, we substitute our solution into this constraint, yielding a quartic polynomial in $\lambda$ that is difficult to solve exactly. However, an approximate solution is often sufficient for stability and accuracy.

Applying the triangle inequality to the norm constraint reveals that the largest, most physically stable root for $\lambda$ must lie in the range $|\mathbf{v}| < \lambda \le |\mathbf{v}| + |\mathbf{b}|$. The method chooses the upper bound as a robust approximation:

$$
{{numeq}}{eq:orientation:lambda_approximation}
\lambda \approx |\mathbf{v}| + |\mathbf{b}|
$$

> **Key Result**: The approximation $\lambda \approx |\mathbf{v}| + |\mathbf{b}|$ provides a fast, closed-form solution that is extremely accurate for stiff materials and coincides with the exact solution in the rest state. The paper demonstrates that this orientation update rule is well-defined and can be used as a proper material model, exhibiting material frame indifference (invariant under translation and rotation) and producing no artificial strain.

#### Exact Solution via Fixed-Point Iteration

For applications requiring strict adherence to the traditional Cosserat energy model, the approximate $\lambda$ can serve as an initial guess for a fixed-point iteration that converges to the exact solution:

$$\lambda_{fp} = f(\lambda) = \sqrt{|\mathbf{v}\mathbf{b}\mathbf{e}_3 + \lambda\mathbf{b}|^2 + |\mathbf{v}|^2}$$

This operator satisfies the Banach fixed-point theorem with gradient $|\nabla_\lambda f(\lambda)| < 1$, ensuring rapid convergence. The procedure starts with $\lambda \approx |\mathbf{v}| + |\mathbf{b}|$ and repeatedly applies $\lambda_{new} = f(\lambda_{old})$ until convergence.

-----

### The Full Algorithm

The complete algorithm for a single time step is a straightforward process that alternates between the position and orientation solves.


<figure><img src="img/lec_thin_shells/stable_algo.png"><figcaption><b>{{fig}}{fig:stable_cosserat_rods_algo}[Main Algorithm]</b>  The algorithm uses VBD for position updates and IPC for collision handling.</figcaption></figure>
This split optimization scheme is not only stable even with large time steps and high stiffness, but its local, iterative nature makes it "embarrassingly parallel" and thus extremely well-suited for high-performance GPU implementations. The result is a method that can be orders of magnitude faster and more robust than previous approaches.

