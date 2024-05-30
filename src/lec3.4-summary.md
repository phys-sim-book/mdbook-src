## Summary

- After examining the convergence issues of traditional Newton's method, even on smooth convex energies, we introduced a backtracking **line search** scheme for minimizing the Incremental Potential of Implicit Euler time integration, ensuring global convergence.
- To guarantee the discovery of a positive step size, the Incremental Potential Hessian is projected onto a nearby Symmetric Positive Definite (SPD) matrix. This **SPD projection** is efficiently achieved by eliminating the negative eigenvalues of the Hessian matrices for each non-convex energy stencil, involving only a few nodes.
- A **convergence criterion** that provides a more intuitive and consistent method for setting tolerance is also introduced, utilizing the Newton search direction.

In the next lecture, we will conclude with a clear demonstration of all the covered topics through a simple 2D case study.
