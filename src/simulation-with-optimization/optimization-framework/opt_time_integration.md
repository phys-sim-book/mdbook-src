## Optimization Time Integrator

With the backward Euler method, each timestep necessitates solving a nonlinear system of equations, as outlined in Equation {{eqref:eq:lec1:system_IE}}. Effectively, this equates to addressing an optimization problem stated as:
$$
\begin{aligned}
x^{n+1} &= \text{arg}\min_x E(x) \\
\text{where} \ E(x) &= \frac{1}{2} \|x - \tilde{x}^n\|^2_M + \Delta t^2 P(x).
\end{aligned}
{{numeq}}{eq:lec2:optimization_IE}
$$
Here, \\(\\tilde{x}^n = x^n + \\Delta t v^n\\), \\(\\frac{1}{2} \\|x - \\tilde{x}^n\\|^2_M = \\frac{1}{2} (x - \\tilde{x}^n)^T M (x - \\tilde{x}^n)\\) represents the inertia term, \\(P(x)\\) stands for the potential energy for forces \\(f(x)\\) with \\(\\frac{\\partial P}{\\partial x}(x) = -f(x)\\), and \\(E(x)\\) is known as the **Incremental Potential**. At the local minimum of \\(E(x)\\), \\(\\frac{\\partial E}{\\partial x}(x^{n+1}) = 0\\), corresponding to Equation {{eqref:eq:lec1:system_IE}}.

Viewing time integration as an optimization problem enables us to utilize well-established optimization methods to robustly acquire the solutions. It also allows for a consistent framework for modeling more complex physical phenomena.

> **{{def}}{def:lec2:conserv_force}[Conservative Forces]**
Forces \\(f(x)\\) for which a potential energy \\(P(x)\\) exists such that \\(\\frac{\\partial P(x)}{\\partial x} = -f(x)\\), are termed **conservative forces**. Both common elasticity forces and body forces such as gravity are examples of conservative forces. They can be easily integrated into the optimization framework by adding the potential energy term into the Incremental Potential.

> **{{rem}}{rem:lec2:gravity}[The gravitational force]**
The gravitational force acting on an object of mass \\(m\\) (represented by the force \\(F = -mg\mathbf{z}\\)) at a height \\(h\\) above the Earth's surface, where \\(g\\) is the acceleration due to gravity and \\(\mathbf{z}\\) is the upward-pointing unit vector, corresponds to the gravitational potential energy \\(U = mgh\\). Here, \\(U\\) is the work done against gravity to move the object from a reference point (at \\(h = 0\\)) to height \\(h\\). The force is the negative gradient of the energy with respect to the position (written mathematically as \\(F = -\nabla U\\)), which confirms the principle of conservation of energy. Taking the derivative of \\(U\\) with respect to \\(h\\), we obtain \\(\nabla U = mg\mathbf{z}\\), and thus \\(F = -\nabla U = -mg\mathbf{z}\\), which matches our starting expression for the force.

> **{{rem}}{rem:lec2:elasticity}[Elasticity]**
Elasticity is the capacity of a solid object to maintain its resting shape in response to external forces. Under the influence of elasticity, the sample points on the same solid will be *bound* together during the simulation. A more rigid solid will have a stiffer elasticity energy, providing a larger elasticity force for the same degree of deformation, thereby aiding in the restoration of the resting shape. The Armadillo slingshot example ({{ref: fig:lec1:slingshot}}) demonstrates typical elasticity effects. Elasticity is a common property across all solids, regardless of their geometric form, and whether they are intuitively rigid or non-rigid, e.g., metal, wood, soft tissue, rubber, cloth, hair, sand, etc.
