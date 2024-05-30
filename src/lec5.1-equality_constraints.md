## Equality Constraint Formulation

Dirichlet boundary conditions (BC), when integrated into the optimization time integrator, are represented as linear equality constraints:
$$
    A x = b,
    {{numeq}}{eq:lec5:BC_x}
$$
In this equation, the matrix \\(A\\) is a \\(m \times dn\\) matrix, where \\(m \leq dn\\). This matrix functions to select the degrees of freedom (DOFs) at the nodes that are subject to the boundary conditions. The vector \\(b\\) is a \\(m \times 1\\) vector, which specifies the precise spatial values that are prescribed by these conditions. 

> {{exp}}{exp:lec5:dbc}[Sticky Dirichlet Boundary Condition]
For a 2D system containing two nodes \\((x_{11}, x_{12})\\) and \\((x_{21}, x_{22})\\), to fix the second node at position \\((1, 2)\\), the boundary condition (Equation {{eqref: eq:lec5:BC_x}}) can be expressed as
$$
    \begin{bmatrix}
      0 & 0 & 1 & 0 \\
      0 & 0 & 0 & 1
    \end{bmatrix}  
    \begin{bmatrix}
      x_{11}\\
      x_{12}\\
      x_{21}\\
      x_{22}\\
    \end{bmatrix}
    = 
    \begin{bmatrix}
      1\\
      2
    \end{bmatrix}.
$$

The two most common types of Dirichlet boundary conditions are sticky and slip:

**Sticky Boundary Conditions**: These conditions effectively fix the position of certain nodes within a time step. They are characterized by a block-wise constraint Jacobian matrix \\(A\\). In this matrix, each set of \\(d\\) rows includes exactly one \\(d \times d\\) identity matrix. The rest of the matrix consists of zero matrices. This configuration is illustrated in {{ref: exp:lec5:dbc}}. The implementation of sticky boundary conditions ensures that the specified nodes remain stationary, adhering to the prescribed positions during the simulation.

**Slip Boundary Conditions**: These conditions are designed to constrain each boundary condition (BC) node within a specific linear subspace, such as a plane or a line, which may not necessarily be axis-aligned. As an example, consider planar slip boundary conditions. Here, for each BC node, there is a corresponding row in the matrix \\(A\\) that contains the normal vector of the plane. This vector occupies the columns corresponding to the BC node, as detailed in {{ref: exp:lec5:slip}}. Such conditions allow the nodes to move, but only within the defined linear subspace, thus adding a layer of complexity and realism to the simulation.

> {{exp}}{exp:lec5:slip}[Slip Dirichlet Boundary Condition]
For the same two-node system in {{ref: exp:lec5:dbc}}, to constrain the first node in the line with equation \\(2x + 3y = 4\\), the constraint (Equation {{eqref: eq:lec5:BC_x}}) can be expressed as
$$
    \begin{bmatrix}
      2 & 3 & 0 & 0
    \end{bmatrix}  
    \begin{bmatrix}
      x_{11}\\
      x_{12}\\
      x_{21}\\
      x_{22}\\
    \end{bmatrix}
    = 4.
$$

At the start of each time step, if we are given that all boundary conditions are satisfied, then the goal during optimization is simply to maintain the positions of the boundary condition nodes. This is represented as:
$$
    A \Delta x = 0.
    {{numeq}}{eq:lec5:BC_dx}
$$
Here, \\(\Delta x\\) is the search direction in each optimization iteration. Maintaining this condition ensures that any updated nodal position \\(x + \alpha \Delta x\\), with \\(\alpha\\) being the step size from line search, still satisfies the boundary conditions:
$$
    A (x + \alpha \Delta x) = b.
$$
This guarantees the adherence to boundary conditions throughout the optimization process.

To enforce the linear equality constraints (Equation {{eqref: eq:lec5:BC_dx}}) for sticky DBC in a time step, we address this in each Newton iteration while solving for the search direction \\( \Delta x \\). This process involves forming the Lagrangian with a quadratic approximation to the Incremental Potential:

$$
    L(\Delta x, \lambda) = \frac{1}{2}\Delta x^T H \Delta x + g^T \Delta x + \lambda^T A\Delta x,
$$

Here, \\( \lambda \\) is the \\( m\times 1 \\) Lagrange multiplier vector. The gradient and Hessian of the Incremental Potential are denoted by \\( g \\) and \\( H \\), respectively.

The solution is approached through a max-min optimization problem:

$$
    \max_\lambda \min_{\Delta x} L(\Delta x, \lambda),
$$

which leads to the formulation of a Karush-Kuhn-Tucker (KKT) system:

$$
    \begin{bmatrix}H & A^T\\ A & \end{bmatrix} \begin{bmatrix}\Delta x\\ \lambda \end{bmatrix} = \begin{bmatrix} -g \\ 0\end{bmatrix}.
    {{numeq}}{eq:lec5:BC_KKT}
$$

Solving this KKT system is essential to determine the search direction. Note that this system is not Symmetric Positive Definite (SPD) and its size increases with the number of BC nodes.

