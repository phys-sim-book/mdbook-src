## Self-Contact

With triangle discretization, the boundary of the domain is approximated as a polyline formed by a set of edges. Let us denote this set of boundary edges as $\mathcal{E}$, and the barrier potential becomes:

$$
\begin{aligned}
    & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{\mathbf{X}_2 \in \Gamma_C - \mathcal{N}(\mathbf{X})} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    \approx & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{e \in \mathcal{E} - I(\mathbf{X})} \min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|, \hat{d}\right) ds(\mathbf{X}) \\
    = & \int_{\Gamma_C} \frac{1}{2} b\left(\min_{e \in \mathcal{E} - I(\mathbf{X})} d^\text{PE}(\mathbf{x}(\mathbf{X}, t), e), \hat{d}\right) ds(\mathbf{X}).
\end{aligned}
$$

Here, $I(\mathbf{X})$ is the set of edges that contain $\mathbf{X}$. Completely ignoring these edges is a specific choice of $\mathcal{N}(\mathbf{X})$ under the current discretization. The term $\min_{\mathbf{X}_2 \in e} \| \mathbf{x}(\mathbf{X}, t) - \mathbf{x}(\mathbf{X}_2, t)\|$ is simply the point-edge distance $d^\text{PE}(\mathbf{x}(\mathbf{X}, t), e)$, which can be calculated as either a point-point distance or a point-line distance depending on the relative positions of the point and the edge.

As we know, the barrier energy density function $b$ is already a smooth approximation to the discontinuous normal contact forces that prevent interpenetration between two colliding points. However, when considering self-contact between discrete surfaces (piecewise linear here), the non-smooth $\min$ operator on point-edge distances is inevitable. This non-smoothness can still pose challenges for optimization time integrators.

To obtain a smooth barrier potential even in the case of piecewise linear boundaries, we first transform the $\min$ operator to a $\max$ operator, as the energy density function $b$ is a non-ascending function everywhere in the domain. This gives us:

$$
\int_{\Gamma_C} \frac{1}{2} \max_{e \in \mathcal{E} - I(\mathbf{X})} b( d^\text{PE}(\mathbf{x}(\mathbf{X}, t), e), \hat{d}) \, ds(\mathbf{X}).
$$

Next, we need to smoothly approximate the $\max$ operator. A straightforward choice is to use the smooth max function, such as the $p$-norm function:

$$
\max(a_1, a_2, ..., a_n) \approx \left(a_1^p + a_2^p + \ldots + a_n^p\right)^\frac{1}{p},
$$

with $p > 0$ sufficiently large. However, the exponent $\frac{1}{p}$ will couple multiple inputs together, increasing the stencil size and making the Hessian less sparse, which will make the simulation more computationally expensive.

Fortunately, due to the local support of $b$, where the contact force only exists for distances smaller than $\hat{d}$, using $p = 1$ is sufficient. With a relatively small $\hat{d}$, there will only be some redundant contact forces at the interface of boundary elements ({{ref: fig:lec20:corner}}).

<figure>
    <center>
    <img src="img/lec20/corner.jpg" width="300">
    </center>
    <figcaption><b>{{fig}}{fig:lec20:corner}</b> In this simple two-edge illustration, the yellow and green regions are only counted once by the summation, but the blue region and the yellow-green overlap are counted twice. If we subtract once the blue region, then for the right-top boundary (convex), it becomes perfect, but for the left-bottom boundary (concave), we can still see some overlap that are counted twice. </figcaption>
</figure>

Since the overlapping supports of $b$ from multiple boundary elements can be clearly identified, it is also possible to subtract the redundant barrier potentials in those regions, as discussed in detail in {{#cite li2023convergent}}. For this book, let us keep it simple by using $p = 1$ with the $p$-norm formulation, which is just summation:

$$
\begin{aligned}
   & \int_{\Gamma_C} \frac{1}{2} \max_{e \in \mathcal{E} - I(\mathbf{X})} b( d^\text{PE}(\mathbf{x}(\mathbf{X}, t), e), \hat{d}) \, ds(\mathbf{X}) \nonumber \\ 
   &\approx \int_{\Gamma_C} \frac{1}{2} \sum_{e \in \mathcal{E} - I(\mathbf{X})} b( d^\text{PE}(\mathbf{x}(\mathbf{X}, t), e), \hat{d}) \, ds(\mathbf{X}).
\end{aligned}
$$

Approximating the integral under triangle discretization and picking the end points of each boundary edge as the quadrature points, we obtain the fully discrete form:

$$
\begin{aligned}
    & \int_{\Gamma_C} \frac{1}{2} \sum_{e \in \mathcal{E} - I(\mathbf{X})} b( d^\text{PE}(\mathbf{x}(\mathbf{X}, t), e), \hat{d}) \, ds(\mathbf{X}) \\
    \approx &
    \sum_{\hat{a}} \frac{\|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} - 1} \| + \|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} + 1} \|}{4} \sum_{e \in \mathcal{E} - I(\mathbf{X}_{\hat{a}})} b( d^\text{PE}(\mathbf{x}_{\hat{a}}, e), \hat{d}).
\end{aligned}
{{numeq}}{eq:lec20:barrier_discrete}
$$

Similar to the solid-obstacle contact cases, $\mathbf{T}_C$ can be derived by taking the derivative of the whole contact potential with respect to the nodal degrees of freedom (DOFs).
