# Linear System

**Author of this lecture: [Tianyi Xie](https://xpandora.github.io/), University of California, Los Angeles*

In the context of simulation with optimization, solving linear systems is a fundamental step for Newton-type methods. At each iteration (see {{ref: alg:lec3:PN_method_IE}}), we need to compute a search direction $p$ as:

$$
p = -P^{-1} \nabla E(x),
$$

where $P$ is typically the Hessian (or a positive-definite proxy) of the incremental potential, and $\nabla E(x)$ is its gradient at the current iterate. In practice, $P$ is often a large, sparse, symmetric positive definite (SPD) matrix. This is equivalent to solving a linear system of the form:

$$
A x = b,
$$

where $A = P$, $x = p$, and $b = -\nabla E(x)$. 

Efficiently solving these linear systems is crucial for the performance of the optimization time integrator. Depending on the problem size and structure, both direct and iterative solvers are commonly used. The following sections introduce the basic structure of these linear systems and discuss common solution strategies.