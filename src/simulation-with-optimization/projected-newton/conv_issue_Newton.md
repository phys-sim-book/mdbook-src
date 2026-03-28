## Convergence Issue of Newton's Method

In addressing the minimization problem presented by implicit Euler time integration (referenced in Equation {{eqref: eq:lec2:optimization_IE}}), employing Newton's method (outlined in {{ref: alg:lec1:newton_method_IE}}) is a viable strategy for the resultant system of nonlinear equations. This involves setting the gradient of the Incremental Potential Energy to zero:

$$
\nabla E(x) = 0.
$$

However, the application of this method to cases such as nonlinear elasticity, particularly in the Neo-Hookean model, does not always guarantee convergence. The presence of truncation errors, especially in scenarios involving large time steps or significant deformations, can adversely affect the convergence process.

> {{exp}}{exp:lec3:newton_conv_issue}[Illustration of Newton's Convergence Issue]
To elucidate the issue of Newton's method non-convergence, let's consider a one-dimensional minimization problem characterized by the objective function:
$$
f(x) = \ln\left(e^{-x}+e^{x}\right).
$$
We can evaluate the function at $x=2$ and approximate it using a quadratic energy $g(x)$, which is defined as:
$$
g(x) = f(2) + f'(2)(x-2) + \frac{1}{2}f''(2)(x-2)^2.
$$
The joint plot of $f(x)$ and $g(x)$ ({{ref: fig:lec3:newton_issue_plot}}) distinctly exhibits that the next iteration would exceed the actual target, landing at a point ($x=-11.645$) further from the actual solution at $x=0$. The subsequent iterations amplify this deviation, leading to a trajectory that diverges. It's worth noting that this demonstration involves a convex function $f(x) = \ln\left(e^{-x}+e^{x}\right)$. The problem can become even more complex when Newton's method is applied to non-convex elasticity energies. <figure>
    <center>
    <img src="img/lec3/newton_issue_plot.jpg" width="350">
    </center>
    <figcaption><b>{{fig}}{fig:lec3:newton_issue_plot}</b> An iteration of Newton's method for $\min_x E(x) = \ln\left(e^{-x}+e^{x}\right)$ at $x=2$. </figcaption>
</figure>

> **{{rem}}{rem:lec3:convexity}[Convexity of Energies]** Convex functions are characterized by symmetric and positive-definite (SPD) second-order derivatives throughout their domain. Conversely, the energy in most models of nonlinear elasticity used in computer graphics is rotation invariant. This implies that the energy value remains unchanged regardless of the rotational orientation of objects or elements. Such rotation invariance leads to **non-convexity**, making the optimization process more complex.


> {{def}}{def:lec3:spd}[Symmetric Positive-Definiteness]
> A square matrix $A \in \mathbb{R}^{n\times n}$ is symmetric positive-definite if 
> - $A = A^T$, and
> - $v^TAv > 0$ for all $v \in \mathbb{R}^n, v \neq 0$.

Unlike directly solving nonlinear equations, a minimization problem provides an energy measure that enables the assurance of global convergence using a technique called line search.
