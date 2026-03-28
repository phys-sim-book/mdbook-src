## Axis-Aligned Slip DBC

Axis-Aligned slip Dirichlet boundary conditions (DBC) uniquely restrict the movement of certain nodes to linear subspaces that are aligned with the axes. For instance, these constraints could limit motion to lines parallel to the x-axis or planes parallel to the yz-plane. An advantageous aspect of Axis-Aligned slip DBC is that their constraint Jacobians bear resemblance to those of sticky DBCs. Consequently, they can be efficiently managed using the same DOF elimination method. 

> {{exp}}{exp:lec6:axis_aligned_slip_DBC}[Axis-Aligned Slip DBC]
Consider the previously mentioned two-node system in a 2D space, as referenced in the slip DBC example ({{ref: exp:lec5:slip}}). To apply a slip DBC that constrains the first node, represented by coordinates \\((x_{11}, x_{12})\\), to move only along the line \\(y = 3\\), we express this constraint as a linear equality:
$$
    \begin{bmatrix}
      0 & 1 & 0 & 0
    \end{bmatrix}  
    \begin{bmatrix}
      x_{11}\\
      x_{12}\\
      x_{21}\\
      x_{22}\\
    \end{bmatrix}
    = 3.
$$
Then similar to sticky DBC, in a time step where this slip DBC is already satisfied, assume we have
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
we can solve the system
$$
    \begin{bmatrix}
      4 & 0 & -1 & -1 \\
      0 & 1 & 0 & 0 \\
      -1 & 0 & 4 & -1 \\
      -1 & 0 & -1 & 4
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
      0\\
      -3\\
      -4
    \end{bmatrix}
$$
for search direction so that \\(\Delta x_{12} = 0\\) and the first node will stay on the \\(y=3\\) line for arbitrary step size since its \\(y\\) coordinate will not vary.
