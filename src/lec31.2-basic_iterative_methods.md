## Basic Iterative Methods

For large or highly sparse linear systems, direct solvers may become impractical due to memory or computational constraints. In such cases, iterative methods provide an alternative by generating a sequence of approximate solutions that (ideally) converge to the true solution. This section introduces two of the most fundamental iterative methods: Jacobi and Gauss-Seidel.

### Jacobi Method

The Jacobi method is a simple, perfectly parallelizable approach for solving $A x = b$. At each iteration, every component of $x$ is updated independently using only the values from the previous iteration. Let $A = D + (L + U)$, where $D$ is the diagonal part of $A$, $L$ is the strictly lower triangular part, and $U$ is the strictly upper triangular part. The update rule is:
$$
x^{(k+1)} = D^{-1} (b - (L + U)x^{(k)}),
{{numeq}}{eq: lec31:jacobi}
$$
or, component-wise:
$$
x_i^{(k+1)} = \frac{1}{A_{ii}} \left( b_i - \sum_{j \neq i} A_{ij} x_j^{(k)} \right), \quad i = 1, \ldots, n.
$$
> **{{rem}}[Parallelism]**
> All components $x_i$ can be updated simultaneously in the Jacobi method, making it well-suited for parallel computation.

### Gauss-Seidel Method

The Gauss-Seidel method improves upon Jacobi by using the most recently updated values as soon as they are available. This means each new $x_i^{(k+1)}$ is immediately used in subsequent updates within the same iteration. The update rule is:
$$
x^{(k+1)} = (D + L)^{-1} (b - U x^{(k)}),
{{numeq}}{eq: lec31:forward_gs}
$$
or, component-wise:
$$
x_i^{(k+1)} = \frac{1}{A_{ii}} \left( b_i - \sum_{j=1}^{i-1} A_{ij} x_j^{(k+1)} - \sum_{j=i+1}^n A_{ij} x_j^{(k)} \right), \quad i = 1, \ldots, n.
$$
A **backward Gauss-Seidel** iteration can also be defined, where the updates sweep from $i = n$ down to $1$ in each iteration:
$$
x^{(k+1)} = (D + U)^{-1} (b - L x^{(k)}),
{{numeq}}{eq: lec31:backward_gs}
$$
or, component-wise:
$$
x_i^{(k+1)} = \frac{1}{A_{ii}} \left( b_i - \sum_{j=i+1}^{n} A_{ij} x_j^{(k+1)} - \sum_{j=1}^{i-1} A_{ij} x_j^{(k)} \right), \quad i = n, n-1, \ldots, 1.
$$
> **{{rem}}[Sequential Update]**
> The Gauss-Seidel method typically converges faster than Jacobi, as it incorporates the latest information within each iteration. However, the updates are inherently sequential.

### Convergence Analysis

All three methods—Jacobi, forward Gauss-Seidel, and backward Gauss-Seidel can be written in the form:
$$
x^{(k+1)} = G x^{(k)} + c,
{{numeq}}{eq: lec31:convergence_form}
$$
where $G$ is the iteration matrix. Specifically,
$$
\begin{aligned}
    G_{JA} &= I - D^{-1}A, \\
    G_{FGS} &= I - (D + L)^{-1}A, \\
    G_{BGS} &= I - (D + U)^{-1}A,
\end{aligned}
$$
for the Jacobi, forward Gauss-Seidel, and backward Gauss-Seidel iterations, respectively. The convergence of these methods depends on the spectral radius $\rho(G)$ (the largest absolute value of the eigenvalues of $G$). The method converges if and only if $\rho(G) < 1$.

A sufficient condition for convergence of both methods is that $A$ is **strictly diagonally dominant** (i.e., $|A_{ii}| > \sum_{j \neq i} |A_{ij}|$ for all $i$). For the Gauss-Seidel method, convergence is also guaranteed if $A$ is symmetric positive definite (SPD).

### Termination Criteria

In practice, iterative methods are terminated when the solution is deemed sufficiently accurate rather than running indefinitely. Common termination criteria include:

1. **Residual-based:** Stop when $\|r^{(k)}\| = \|b - Ax^{(k)}\| < \epsilon$ or $\|r^{(k)}\| / \|b\| < \epsilon$ for a prescribed tolerance $\epsilon$.
2. **Solution change:** Stop when $\|x^{(k+1)} - x^{(k)}\| < \epsilon$ or $\|x^{(k+1)} - x^{(k)}\| / \|x^{(k)}\| < \epsilon$ indicating convergence.
3. **Maximum iterations:** Stop after a predetermined number of iterations to prevent infinite loops.

The relative residual-based criterion $\|r^{(k)}\| / \|b\| < \epsilon$ is most commonly used as it directly measures how well the current solution satisfies the original linear system, considering the scale of the problem.