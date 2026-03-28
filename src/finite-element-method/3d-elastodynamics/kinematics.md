## Kinematics

Similar to 2D triangle elements, we use $\beta, \gamma, \tau \in [0,1]$ with $\beta + \gamma + \tau \leq 1$ to express the material space coordinates of an arbitrary point $\mathbf{X}$ in the tetrahedral element defined by vertices $\mathbf{X}_1, \mathbf{X}_2, \mathbf{X}_3,$ and $\mathbf{X}_4$ as follows:
$$
\begin{aligned}
    \mathbf{X}(\beta, \gamma, \tau) & = \mathbf{X}_1 + \beta (\mathbf{X}_2 - \mathbf{X}_1) + \gamma (\mathbf{X}_3 - \mathbf{X}_1) + \tau (\mathbf{X}_4 - \mathbf{X}_1) \\
    & = (1 - \beta - \gamma - \tau) \mathbf{X}_1 + \beta \mathbf{X}_2 + \gamma \mathbf{X}_3 + \tau \mathbf{X}_4.
\end{aligned}
$$
Here, $\mathbf{X}$ is a linear function of $(\beta, \gamma, \tau)$. Using linear shape functions, the approximate world-space coordinate $\hat{\mathbf{x}}$ is also a linear function of $(\beta, \gamma, \tau)$:
$$
\begin{aligned}
    \mathbf{x}(\beta, \gamma, \tau) \approx \hat{\mathbf{x}}(\beta, \gamma, \tau) = (1 - \beta - \gamma - \tau) \mathbf{x}_1 + \beta \mathbf{x}_2 + \gamma \mathbf{x}_3 + \tau \mathbf{x}_4,
\end{aligned}
$$
where $\mathbf{x}(\mathbf{X}_i)$ is denoted as $\mathbf{x}_i$. This implies that the shape functions are:
$$
\begin{aligned}
    N_1(\beta, \gamma, \tau) &= 1 - \beta - \gamma - \tau, \\
    N_2(\beta, \gamma, \tau) &= \beta, \\
    N_3(\beta, \gamma, \tau) &= \gamma, \\
    N_4(\beta, \gamma, \tau) &= \tau.
\end{aligned}
$$
