## Change of Variables

### Challenges with General Slip DBCs and the DOF Elimination Method

When dealing with general linear equality constraints, such as slip DBCs that aren't axis-aligned, the direct Degree of Freedom (DOF) elimination method faces certain limitations. This becomes evident particularly when \\( AA^T \\) is not an \\( m \times m \\) identity matrix. According to the Karush-Kuhn-Tucker (KKT) system (Equation {{eqref: eq:lec5:BC_KKT}}), the Lagrange multiplier vector \\( \lambda \\) can be solved as follows:

$$
    \lambda = -(AA^T)^{-1} A H \Delta x - (AA^T)^{-1} Ag.
    {{numeq}}{eq:lec6:slipBC_lambda}
$$

When we substitute Equation {{eqref: eq:lec6:slipBC_lambda}} back into the KKT system, it results in:

$$
    (I - A^T (AA^T)^{-1} A) H\Delta x = (I - A^T (AA^T)^{-1} A)(-g),
    {{numeq}}{eq:lec6:slipBC_lambda_solved}
$$

This leads to an under-constrained system. The key challenge here is that \\( I - A^T (AA^T)^{-1} A \\) does not possess a special structure that can be conveniently exploited to derive an equivalent, non-singular system while still satisfying the constraints. This makes the direct application of the DOF elimination method impractical for general slip DBCs.

### Simplifying Constraints Using Singular Value Decomposition

Our approach involves transforming the degrees of freedom (DOF) into a new set of variables, making the constraints as straightforward as those in sticky DBC. To achieve this, we employ singular value decomposition (SVD) on the constraint Jacobian matrix \\( A \\). The SVD of \\( A \\) is expressed as:
$$
    A = U S V^T.
$$
Here, \\( U \\) is a \\( m \times m \\) orthogonal matrix, \\( V \\) is a \\( dn \times dn \\) orthogonal matrix, and \\( S \\) is a \\( m \times dn \\) diagonal matrix.

By defining \\( y = V^T \Delta x \\), we can reframe the Karush-Kuhn-Tucker (KKT) system (Equation {{eqref: eq:lec5:BC_KKT}}) into a new format:

$$
    \begin{bmatrix}V^T H V & S^T\\ S & 0\end{bmatrix} \begin{bmatrix}y\\ \lambda' \end{bmatrix} = \begin{bmatrix} -V^T g \\ 0\end{bmatrix}.
    {{numeq}}{eq:lec6:BC_KKT_SVD}
$$

In this transformed system, \\( \lambda' = U^T \lambda \\). Notably, the presence of the diagonal matrix \\( S \\) in the off-diagonal blocks allows the direct application of the DOF elimination method. Once we solve for \\( y \\), the original variable \\( \Delta x \\) is easily recovered through the matrix-vector product \\( \Delta x = V y \\).

> **{{rem}}{rem:lec6:SVD_limitations}[Limitations of Using SVD for DOF Elimination]**
While we utilized singular value decomposition (SVD) to illustrate the concept, it's important to recognize the limitations of applying SVD in practice, especially on large matrices. There are two primary concerns:
> 1. **Intractability with Large Matrices:** Performing SVD on matrices of substantial size can be computationally challenging and often impractical.
> 2. **Impact on Computational Efficiency:** The Incremental Potential Hessian \\( H \\) typically exhibits sparsity, making it efficient to factorize in linear solves during simulations. However, if the resulting \\( V \\) from the SVD is dense, then \\( V^T H V \\) will also be dense. This not only slows down the computation but also significantly increases the cost of linear solves.
>
> It's crucial to note that the new basis set (the column vectors of \\( V \\)) needs to be linearly independent but does not necessarily have to be orthonormal. This insight opens up the possibility of identifying a sparse basis set. Such a set can maintain computational efficiency when dealing with general linear equality constraints. For a practical example of this approach, see @@chen2022unified.