## General Slip DBC

Fortunately, for constraints like slip DBCs that are decoupled per node, SVD simply results in block-diagonal $U$ and $V$ which could be constructed procedurally in an efficient way.
3D planar slip DBC at node $i$ can be expressed as
$$
    \mathbf{n}_i^T (\mathbf{x}_i - \mathbf{x}_i') = 0,
$$
where $\mathbf{n}_i$ is the normal of the plane that node $i$ is slipping, and $\mathbf{x}_i'$ is an arbitrary point on that plane. 
As discussed near Equation {{eqref: eq:lec5:BC_dx}}, if at the beginning of the time step node $i$ is already on the plane, the constraint simplifies to
$$
    \mathbf{n}_i^T \Delta \mathbf{x}_i = 0.
$$
Then performing SVD on the row vector $\mathbf{n}_i^T$, we obtain
$$
    \mathbf{n}_i^T = \mathbf{U}_i \mathbf{S}_i \mathbf{V}_i^T = 1 \begin{bmatrix} 1 & 0 & 0\end{bmatrix} \begin{bmatrix} \mathbf{n}_i^T \\ \textbf{m}_i^T \\ \mathbf{l}_i^T \end{bmatrix},
    {{numeq}}{eq:lec6:BC_normal_SVD}
$$
where unit vectors $\mathbf{n}_i$, $\textbf{m}_i$, and $\mathbf{l}_i$ together form an orthonormal basis in 3D. 

Then it becomes clear that globally, $U$ is simply a $m\times m$ identity matrix, $S$ is a $m\times dn$ matrix where every row contains exactly one unit-valued entry in the column corresponding to the first DOF of the slip BC node, and $V$ is a $dn \times dn$ block-diagonal matrix with the $d\times d$ orthonormal blocks only on those corresponding to BC nodes, and $d\times d$ identity matrix elsewhere.

To compute $\textbf{m}_i$ and $\mathbf{l}_i$ from $\mathbf{n}_i$, we first note that there are an infinite number of possible solutions. 
Therefore, we can simply first construct $\textbf{m}_i = \mathbf{n}_i \times \begin{bmatrix}1 & 0 & 0\end{bmatrix}^T$, or $\textbf{m}_i = \mathbf{n}_i \times \begin{bmatrix}0 & 1 & 0\end{bmatrix}^T$ if $\mathbf{n}_i$ is almost colinear with $\begin{bmatrix}1 & 0 & 0\end{bmatrix}^T$, and then construct $\mathbf{l}_i = \mathbf{n}_i \times \textbf{m}_i$.
To obtain $V^T (-g)$, one only needs to left-multiply each $\mathbf{V}_i^T = \begin{bmatrix} \mathbf{n}_i^T \\ \textbf{m}_i^T \\ \mathbf{l}_i^T \end{bmatrix}$ to $-\mathbf{g}_i$. 
As for $V^T H V$, first left-multiply each $\mathbf{V}_i^T$ to every block on the $i$-th block row of $H$ to obtain $V^T H$. Then for the $i$-th block column of $V^T H$, right-multiply $\mathbf{V}_i = \begin{bmatrix} \mathbf{n}_i & \textbf{m}_i & \mathbf{l}_i \end{bmatrix}$ to every block. 
Finally, after solving for $y$ by applying the DOF elimination method on the modified system (Equation {{eqref: eq:lec6:BC_KKT_SVD}}), $\Delta x$ can be obtained by $\Delta x = V y$ with similar block(node)-wise operations.

> {{exp}}{exp:lec6:general_slip_DBC_example}[General Slip DBC]
For the same two-node system in 2D as mentioned in the slip DBC example ({{ref: exp:lec5:slip}}), to constrain the first node $(x_{11}, x_{12})$ inside the $3x + 4y = 2$ line, the slip DBC can be expressed as
$$
    \begin{bmatrix}
      3 & 4 & 0 & 0
    \end{bmatrix}  
    \begin{bmatrix}
      x_{11}\\
      x_{12}\\
      x_{21}\\
      x_{22}\\
    \end{bmatrix}
    = 2
$$
and we can build
$$
    U =1,\
    S = \begin{bmatrix} 
    1 & 0 & 0 & 0
    \end{bmatrix},\
    V^T = \begin{bmatrix} 
    0.6 & 0.8 & & \\ 
    -0.8 & 0.6 & & \\
    & & 1 & \\ 
    & & & 1 \\
    \end{bmatrix}
$$
for changing the basis.
Then in a time step where this slip DBC is already satisfied, assume we have
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
we can compute
$$
    V^T H V = \begin{bmatrix}
      4.96 & 0.28 & 0.2 & 0.2 \\
      0.28 & 3.04 & -1.4 & -1.4 \\
      0.2 & -1.4 & 4 & -1 \\
      0.2 & -1.4 & -1 & 4
    \end{bmatrix}, \quad \text{and} \quad
    V^T g = \begin{bmatrix}
      -1 \\
      2 \\
      3 \\
      4
    \end{bmatrix},
$$
and solve the system
$$
    \begin{bmatrix}
      1 & 0 & 0 & 0 \\
      0 & 3.04 & -1.4 & -1.4 \\
      0 & -1.4 & 4 & -1 \\
      0 & -1.4 & -1 & 4
    \end{bmatrix}  
    \begin{bmatrix}
      y_{11}\\
      y_{12}\\
      y_{21}\\
      y_{22}\\
    \end{bmatrix}
    = 
    \begin{bmatrix}
      0 \\
      -2 \\
      -3 \\
      -4
    \end{bmatrix}
$$
for $y$. Then the search direction can be obtained by $\Delta x = Vy$ so that $3 \Delta x_{11} + 4 \Delta x_{12} = 0$ and so the first node will stay on the $3x + 4y = 2$ line for arbitrary step size.
