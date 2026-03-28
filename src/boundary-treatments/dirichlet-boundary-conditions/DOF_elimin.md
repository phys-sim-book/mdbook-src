## DOF Elimination Method

Considering the simplest sticky Dirichlet boundary condition as an example, its constraint Jacobian \\( A \\) acts as a selection matrix. Consequently, \\( AA^T \\) forms a \\( m \times m \\) identity matrix, and \\( A^T A \\) becomes a \\( dn \times dn \\) diagonal matrix. In this matrix, the entries corresponding to the BC nodes are one, and all other entries are zero.

When we left-multiply \\( A \\) to the first block row of Equation {{eqref: eq:lec5:BC_KKT}}, the resulting equation is:

$$
    \begin{bmatrix}A H & A A^T\\ A & \end{bmatrix} \begin{bmatrix}\Delta x\\ \lambda \end{bmatrix} = \begin{bmatrix} -A g \\ 0\end{bmatrix}.
$$

This manipulation allows us to directly solve for \\( \lambda \\) as:

$$
    \lambda = -A H \Delta x - Ag.
    {{numeq}}{eq:lec5:BC_lambda}
$$

By substituting Equation {{eqref: eq:lec5:BC_lambda}} back into the first block row of Equation {{eqref: eq:lec5:BC_KKT}}, we derive the following equation:

$$
    (I - A^T A) H\Delta x = (I - A^T A)(-g).
    {{numeq}}{eq:lec5:BC_lambda_solved}
$$

Here, left-multiplying by \\((I - A^T A)\\) effectively zeroes out the rows corresponding to the BC nodes. Hence, Equation {{eqref: eq:lec5:BC_lambda_solved}}  represents an under-constrained system. However, the second block row of Equation {{eqref: eq:lec5:BC_KKT}} actually provides us with the values of \\(\Delta x\\) at the BC nodes (so they are not really unknowns). By considering this information, we can rewrite Equation {{eqref: eq:lec5:BC_lambda_solved}} into a Symmetric Positive Definite (SPD) system:

$$
    H_{UB} \Delta x_B + H_{UU} \Delta x_U = -g_U,
$$

where the matrices and vectors are partitioned as follows:

$$
H = \begin{bmatrix}H_{BB} & H_{BU} \\ H_{UB} & H_{UU} \end{bmatrix}, \quad 
\Delta x = \begin{bmatrix} \Delta x_B \\ \Delta x_U \end{bmatrix}, \quad
g = \begin{bmatrix}g_B \\ g_U \end{bmatrix},
$$

and the subscript \\(B\\) denotes the BC nodes. Knowing that \\(\Delta x_B = 0\\), the system simplifies to:

$$
    H_{UU} \Delta x_U = -g_U,
    {{numeq}}{eq:lec5:BC_final_system}
$$

which represents a SPD system that excludes the BC nodes.

### A More Practical Approach

The method outlined above serves primarily for mathematical explanation. In practical applications, constructing Equation {{eqref: eq:lec5:BC_final_system}} is often avoided. This is because it entails reordering degrees of freedom (DOFs) and separating the BC nodes from unconstrained nodes, a process that can be both tedious and inefficient, particularly when the set of Dirichlet nodes varies over time.

To circumvent the need to reorder DOFs, a direct modification of the original linear system can be made to align it with Equation {{eqref: eq:lec5:BC_final_system}}. This adjustment involves setting all entries in the rows corresponding to BC nodes in \\( H \\) and \\( g \\) to \\( 0 \\). Additionally, for the columns associated with BC nodes in \\( H \\), all off-diagonal entries are set to \\( 0 \\) while diagonal entries are assigned \\( 1 \\) or another positive real number to ensure the system remains well-conditioned. After solving this modified system, the resulting values of \\( \Delta x_U \\) are immediately aquired, and all \\( \Delta x_B \\) values are guaranteed to be \\( 0 \\).

> {{exp}}{exp:lec5:DOF_elim_example}[DOF Elimination]
For the problem defined in {{ref: exp:lec5:dbc}} where the second node \\((x_{21}, x_{22})\\) is fixed at \\((1,2)\\) in a 2D two-node system, assuming in a certain iteration of a time step
$$
    H = \begin{bmatrix}
      4 & -1 & -1 & -1 \\
      -1 & 4 & -1 & -1 \\
      -1 & -1 & 4 & -1 \\
      -1 & -1 & -1 & 4
    \end{bmatrix}, \quad \text{and} \quad
    g = \begin{bmatrix}
      1 \\
      2 \\
      3 \\
      4
    \end{bmatrix},
$$
we solve the system
$$
    \begin{bmatrix}
      4 & -1 & 0 & 0 \\
      -1 & 4 & 0 & 0 \\
      0 & 0 & 1 & 0 \\
      0 & 0 & 0 & 1
    \end{bmatrix}  
    \begin{bmatrix}
      \Delta x_{11}\\
      \Delta x_{12}\\
      \Delta x_{21}\\
      \Delta x_{22}\\
    \end{bmatrix}
    = 
    \begin{bmatrix}
      -1\\
      -2\\
      0\\
      0
    \end{bmatrix}.
    {{numeq}}{eq:sec5:BC_projected_system}
$$
for search direction \\(\Delta x\\) so that \\(\Delta x_{21} = \Delta x_{22} = 0\\) and after line search we for sure know that \\((x_{21}, x_{22}) = (1, 2)\\) still holds since $(x_{21} + \alpha \Delta x_{21}, x_{22} + \alpha \Delta x_{22}) = (x_{21}, x_{22})$. Here {{eqref: eq:sec5:BC_projected_system}} is essentially
$$
    \begin{bmatrix} H_{UU} & \\  & I \end{bmatrix} \begin{bmatrix}\Delta x_U \\ \Delta x_B \end{bmatrix} = \begin{bmatrix} -g_U \\ 0\end{bmatrix}
$$

> **{{rem}}{rem:lec5:DOF_elim_limitations}[Limitations of DOF Elimination]**
The DOF elimination method described is effective when sticky BC nodes are established at the beginning of the time step. However, if this is not the case, and the constraint function in Equation {{eqref: eq:lec5:BC_KKT}} has a non-zero right-hand side (rhs), the DOF elimination method becomes inapplicable. The issue here is not the inability to solve for \\( \Delta x \\) under constraints with a non-zero rhs. Rather, the concern is that the resulting \\( \Delta x \\) might not lead to a descent direction in the Incremental Potential. This can result in exceedingly small step sizes after a line search, potentially stalling the optimization process.
>
> Intuitively, if the direction of \\( \Delta x_B \\) is towards the prescribed BC coordinates, it could inadvertently increase the Incremental Potential, which is not adjusted to consider the BCs. Conversely, if \\( \Delta x_B \\) is simply \\( 0 \\) when the BCs are already satisfied, it effectively minimizes the Incremental Potential using a subset of variables, which remains a valid approach.
>
> One might then ask why not adjust the DOFs to meet the BCs before starting the optimization. However, this strategy could lead to infeasible configurations, such as those involving intersections. A viable alternative is to initially apply stiff spring forces to gradually 'drag' the BC nodes to their constrained positions during optimization. After this, switching to the DOF elimination method can enhance convergence. This technique is further discussed in the section [Moving Boundary Conditions*](lec11-mov_DBC.md).
