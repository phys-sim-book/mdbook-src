## Summary

In this section, we explored fundamental approaches for solving large, sparse linear systems of the form $Ax = b$ that arise in optimization-based simulation, particularly when computing search directions in Newton-type methods.

We established the context: at each iteration of projected Newton methods, we solve $Pp = -\nabla E(x)$ where $P$ is typically a symmetric positive definite (SPD) Hessian matrix. This reduces to solving linear systems crucial for optimization time integrators.

Direct solvers provide exact solutions through matrix factorization. For SPD systems, Cholesky decomposition $A = LL^T$ reduces the problem to two triangular solves. While robust and accurate, direct methods become impractical for very large systems due to computational constraints.

Iterative methods offer alternatives for large, sparse systems. Basic methods include Jacobi (naturally parallelizable) and Gauss-Seidel (faster convergence but sequential). Both converge when the spectral radius of their iteration matrices is less than 1. The Conjugate Gradient (CG) method is a more sophisticated approach for SPD systems. CG constructs $A$-conjugate search directions ensuring progress is never undone, achieving remarkable efficiency through orthogonality relationships. In exact arithmetic, CG converges in at most $n$ steps, while in practice it often converges much faster with preconditioning.

The choice between direct and iterative methods depends on problem size, sparsity structure, and accuracy requirements. Direct methods excel for moderate-sized problems requiring high precision, while iterative methods, particularly CG with preconditioning, are essential for large-scale simulations.