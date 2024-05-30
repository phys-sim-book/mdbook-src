## Dirichlet Boundary Conditions

Potential energies aren't the only means of modeling physical phenomena; constraints are equally vital. Let's start by considering the simplest form, **linear equality constraints**. The constrained optimization problem is defined as follows:
$$
    \min_x E(x) \quad s.t. \quad Ax = b,
    {{numeq}}{eq:lec2:LEC_problem}
$$
Here, \\(A\\in \mathbb{R}^{m\\times dn}\\) and \\(b\\in \mathbb{R}^{m}\\) represent \\(m\\) linear equality constraints.

During simulations, it's often necessary to control the position of certain points on a solid at each timestep. This can involve fixing a set of nodes to model immovable objects like the ground or obstacles, or guiding the motion of solids by moving specific nodes along predetermined paths. For example, in the slingshot scenario ({{ref: fig:lec1:slingshot}}), the Armadillo's feet and ears are stationary. This type of control is known as **Dirichlet boundary conditions (BC)**. These conditions can be expressed as linear equality constraints within the optimization time integrator framework.

To put it into perspective, the matrix \\(A\\) in Equation {{eqref:eq:lec2:LEC_problem}} would typically be an \\(m\\times dn\\) matrix (with \\(m\\leq dn\\) and \\(m \\mod d = 0\\)), which selects the coordinates of the BC points. Correspondingly, \\(b\\) would be an \\(m \\times 1\\) vector defining the prescribed locations. By solving the optimization problem, the chosen points are fixed at these specified locations, which can vary from one timestep to the next.

At the local minimum of the problem in Equation {{eqref:eq:lec2:LEC_problem}}, the **KKT condition**
$$
\begin{aligned}
    \nabla E(x) - A^T \lambda = 0 \\
    A x = b
\end{aligned}
{{numeq}}{eq:lec2:LEC_KKT}
$$
is met, where \\(\\lambda \\in \\mathbb{R}^{m}\\) represents the **Lagrange multiplier** vector, comprising all the Lagrange multipliers.

> **{{rem}}{rem:lec2:solving_KKT}[Solving KKT Systems]**
Solving nonlinear optimization problems with equality constraints is feasible by directly addressing the nonlinear KKT (Karush-Kuhn-Tucker) system, as seen in Equation {{eqref:eq:lec2:LEC_KKT}}. Methods like Newton's method are commonly employed for this purpose. However, this approach can be computationally intensive. For boundary conditions, the unique structure of the matrix \\(A\\) can be leveraged, allowing us to resolve the constrained problem in an unconstrained manner. Techniques for this approach will be demonstrated in later lectures.