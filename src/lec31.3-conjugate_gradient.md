## Conjugate Gradient Method

The Conjugate Gradient (CG) method is a powerful iterative algorithm for solving large, sparse linear systems of the form $A x = b$, where $A$ is symmetric positive definite (SPD). Unlike Jacobi and Gauss-Seidel, CG is specifically designed for SPD matrices and is widely used in scientific computing and simulation.


<!-- ### Algorithm Overview

CG generates a sequence of approximate solutions $x_k$ that converge to the true solution. At each iteration, the method constructs a search direction that is $A$-conjugate (orthogonal with respect to the $A$-inner product) to all previous directions, ensuring efficient progress toward the solution.

The basic steps of the CG algorithm are summarized in {{ref: alg:lec31:cg}}
<figure>
    <center>
    <b><figcaption>{{alg}}{alg:lec31:cg}[The Conjugate Gradient Method]</figcaption></b>
    <img src="img/lec31/alg_cg.png" width="650">
    </center>
</figure> -->

### Formulation as Quadratic Optimization

The conjugate gradient method can be understood as an optimization algorithm for minimizing a quadratic function:
$$
    f(x) = \frac{1}{2} x^T A x - b^T x,
$$
where $A$ is SPD. The unique minimizer of $f(x)$ is the solution to $A x = b$.

The steepest descent method updates $x$ along the negative gradient direction $-\nabla f(x) = b - A x$, but this can lead to slow convergence, especially when $A$ is ill-conditioned. The CG method improves upon this by searching along a sequence of directions $p_k$ that are $A$-conjugate, i.e., $p_i^T A p_j = 0$ for $i \neq j$. This property ensures that each step makes progress that is not undone by subsequent steps, and the minimization along each direction is independent of the others.


### Line Search
Suppose we have a sequence of conjugate directions $p_0, p_1, p_2, \ldots$. Minimizing the quadratic function then becomes a problem of finding step sizes for those directions so that $\sum_{i=1}^{n} \alpha_i p_i$ closely approximates the solution $x^*$.

The simplest method is greedy line search: we start at some point $x_0$, pick a direction $p_0$, then minimize $f(x_0 + \alpha_0 p_0)$. This has a simple closed-form solution that does not require matrix inversion:
$$
    \alpha_0 = \frac{p_0^T (b - A x_0)}{p_0^T A p_0}.
$$
Intuitively, we start at $x_0$, choose a direction, and move along it until the objective is minimized in that direction. This is not necessarily the global minimum, but it is progress toward it.

We repeat this procedure: at the new point $x_1 = x_0 + \alpha_0 p_0$, pick a new direction $p_1$, compute $\alpha_1$, and so on.

This iteration process can be summarized as:
$$
\begin{aligned}
\alpha_i &= \frac{p_i^T r_i}{p_i^T A p_i},\\
x_{i+1} &= x_i + \alpha_i p_i,\\
r_{i+1} &= r_i - \alpha_i A p_i
\end{aligned}
{{numeq}}{eq: lec31:step_size}
$$
where $p_0, p_1, p_2, \ldots$ are the search directions. Notice that the residual is updated iteratively: $r_{i+1} = b - A x_{i+1} = b - A (x_i + \alpha_i p_i) = r_i - \alpha_i A p_i$.

### Conjugate Directions
If the directions $p_0, p_1, p_2, \ldots$ are not chosen well, progress will be slow. In particular, gradient descent is slow for ill-conditioned $A$. In contrast, if we pick the directions to be mutually conjugate, there will be no overshoot, and we obtain the global minimum after $n$ steps, where $n$ is the number of dimensions:
$$
    p_i^T A p_j = 0, \quad \text{for any } i \neq j.
$$

Without loss of generality, assume $x_0 = 0$ and set $p_0$ to be the negative gradient of $f$ at $x_0$. This gradient is $A x_0 - b$. Since $x_0 = 0$, we have $p_0 = b$. The other directions will be conjugate to $b$.

Let $r_k$ be the residual at the $k$-th step:
$$
    r_k = b - A x_k.
$$
Note that $r_k$ is the negative gradient of $f$ at $x_k$, so gradient descent would move in the direction $r_k$. Here, we require that the directions $p_k$ be conjugate to each other. We also require that the next search direction is built from the current residual and all previous search directions. We pick the negative gradient $r_k$ as the initial direction and eliminate all previous search directions $p_0, p_1, \ldots, p_{k-1}$ through the Gram–Schmidt process, which gives:
$$
\begin{equation}
    p_k = r_k - \sum_{i=0}^{k-1} \frac{p_i^T A r_k}{p_i^T A p_i} p_i.
\end{equation}
{{numeq}}{eq:lec31:conjugate_direction}
$$

### Simplification
We can simplify the above equations, leading to the final conjugate gradient algorithm.

First, we show that the following equations hold:
$$
\begin{equation}
    p_i^T r_j = 0,\ \forall i < j \quad \text{and} \quad r_i^T r_j = 0,\ \forall i < j.
\end{equation}
{{numeq}}{eq:lec31:lemma}
$$

From the residual update:
$$ 
    r_{j} = r_{j-1} - \alpha_{j-1} A p_{j-1} = r_i - \sum_{k=i}^{j-1} \alpha_{k} A p_{k}.
$$
Taking the dot product with $p_i$:
$$
    p_i^T r_j = p_i^T r_i - \sum_{k=i}^{j-1} \alpha_{k} p_{i}^T A p_{k}.
$$
By the conjugate construction and $\alpha_i = \frac{p_i^T r_i}{p_i^T A p_i}$, we can prove $p_i^T r_j = 0$. The second equation $r_i^T r_j = 0$ also holds, as $r_j$ is a linear combination of $p_0, p_1, ..., p_{j-1}$ by our construction (note $p_0 = r_0$).

Plugging these equations into Equation {{eqref:eq:lec31:conjugate_direction}} yields:
$$
    p_k^T r_k = r_k^T r_k.
$$

Thus, we can simplify the calculation of $\alpha_k$ as:
$$
    \alpha_k = \frac{r_k^T r_k}{p_k^T A r_k}.
$$

We can also simplify $p_{k+1}$ as:
$$
\begin{aligned}
    p_{k+1} &= r_{k+1} - \sum_{i=0}^{k} \frac{p_i^T A r_{k+1}}{p_i^T A p_i} p_i \\
            &= r_{k+1} -  \sum_{i=0}^{k}\frac{r_{k+1}^T A p_i}{p_i^T A p_i} p_i \\
            &= r_{k+1} -  \sum_{i=0}^{k} \frac{r_{k+1}^T (r_i - r_{i+1})}{\alpha_i p_i^T A p_i} p_i \\
            &= r_{k+1} + \sum_{i=0}^{k} \frac{r_{k+1}^T  r_{i+1}}{\alpha_i p_i^T A p_i} p_i \\
            &= r_{k+1} + \sum_{i=0}^{k} \frac{r_{k+1}^T  r_{i+1}}{r_i^T r_i} p_i \\
            &= r_{k+1} + \frac{r_{k+1}^T  r_{k+1}}{r_k^T r_k} p_k \\
\end{aligned}
$$

Now the simplified CG algorithm is summarized in {{ref: alg:lec31:cg}}
<figure>
    <center>
    <b><figcaption>{{alg}}{alg:lec31:cg}[The Conjugate Gradient Method]</figcaption></b>
    <img src="img/lec31/alg_cg.png" width="650">
    </center>
</figure>

### Preconditioning

The convergence rate of CG depends on the condition number of $A$. If $A$ is ill-conditioned, CG may converge slowly. Preconditioning is a technique to accelerate convergence by transforming the system into one with more favorable properties.

A simple and widely used preconditioner is the **diagonal (Jacobi) preconditioner**. Let $M = \operatorname{diag}(A)$ be the diagonal matrix containing the diagonal entries of $A$. The preconditioned CG algorithm solves:
$$
M^{-1} A x = M^{-1} b
$$

In practice, this means that at each iteration, the residual is scaled by $M^{-1}$, which is inexpensive to compute. The preconditioned CG steps are similar to the standard CG algorithm, but with the residuals replaced by their preconditioned versions.
