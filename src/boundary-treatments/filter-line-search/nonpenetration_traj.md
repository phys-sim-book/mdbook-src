## Penetration-free Trajectory

The most straightforward way of defining the motion trajectory between \\(x^n\\) and \\(x^{n+1}\\) at time \\(t^n\\) and \\(t^{n+1}\\) respectively would be the high-dimensional line segment connecting these two configurations. However, although enforcing non-negative signed distances on this trajectory could avoid the tunneling issue in {{ref: exp:lec8:tunneling}}, this strategy could potentially result in unrealistic behaviors as it alters the local optimum of the minimization problem (Equation {{eqref: eq:lec7:constrained_IP}}) in a nonphysical way ({{ref: fig:lec8:altered_min}}).

<figure>
    <center>
    <img src="img/lec8/altered_min.jpg" width="550">
    </center>
    <figcaption><b>{{fig}}{fig:lec8:altered_min}</b> For the setup in the tunneling example, enforcing non-negative signed distance along the motion trajectory approximated by the line segment between $x^n$ and $x^{n+1}$ results in a nonphysical simulation result. </figcaption>
</figure>

A more rigorous definition of the motion trajectory between \\(x^n\\) and \\(x^{n+1}\\) could be
$$
    \left\{ \text{arg}\min_{x} \left. \left( \frac{1}{2}\|x - (x^n+hv^n)\|_M^2 + h^2 \sum P(x) \right) \right| \ h \in [0, t^{n+1} - t^n] \right\}.
$$
However, evaluating the configurations on this trajectory requires solving extra optimization problems, which could significantly complicate the time integration.

Instead, IPC takes the optimization path as an approximation to the motion trajectory. Specifically, for the time step solving from \\(x^n\\) to \\(x^{n+1}\\), if the optimization took \\(l\\) iterations, and each iteration we get iterate \\(x^i\\) after line search, the optimization path is simply the high-dimensional polyline
$$
    \{ (1 - \beta) x^i + \beta x^{i+1} \ | \ \beta \in [0, 1], \ i = 0, 1, 2, ..., l \}.
$$
Now the time integration problem in time step \\(n\\) becomes finding such optimization path \\(x^0, x^1, ..., x^l\\) where \\(x^l\\) locally minimizes the Incremental Potential (Equation {{eqref: eq:lec7:barrier_IPC}}) subject to
$$
    d_{jk}((1 - \beta) x^i + \beta x^{i+1}) > 0 \\ \forall \ \text{node} \ j, \ \text{obstacle} \ k, \ \beta \in [0, 1], \text{and} \ i = 0, 1, 2, ..., l.
$$
This enables enforcing the non-negative distance constraints per optimization iteration on the line segment between \\(x^i\\) and \\(x^{i+1}\\), which will not alter the local optimum of the time integration problem, and can be handled efficiently.

Recall from {{ref: alg:lec3:line_search}} that the line search scheme updates the iterate as \\(x^{i+1} \leftarrow x^i + \alpha p\\), which means \\(x^{i+1} - x^{i} = \alpha p\\). Therefore, given an interpenetration-free \\(x^i\\), to ensure all the configurations on the line segment between \\(x^i\\) and \\(x^{i+1}\\) are interpenetration-free, we just need to find such \\(\alpha\\) that makes sure 
$$
d_{jk}(x^i + \beta p) \geq 0 \quad \forall \ \text{node} \ j, \ \text{obstacle} \ k, \ \text{and} \ \beta \in [0, \alpha].
$$
Based on the intuition that a sufficiently small \\(\alpha\\) could definitely make this happen, we can simply calculate an upper bound of such \\(\alpha\\) in every iteration, and make sure the backtracking line search results in a step size smaller than this upper bound. This upper bound can be conveniently calculated with continuous collision detection (CCD).

> **{{def}}{def:lec8:ccd}[Continuous Collision Detection (CCD)]**
> For a distance function \\(d_{jk}(x + \alpha p)\\) defined with the initial interpenetration-free configuration of the solids and obstacles \\(x\\), their intended displacement \\(p\\), and the step size \\(\alpha\\), CCD calculates the step size \\(\alpha^C_{jk}\\) given \\(x\\) and \\(p\\) such that
$$
    d_{jk}(x + \alpha p) > 0 \quad \forall \ \alpha \in [0, \alpha^C_{jk}).
    {{numeq}}{eq:lec8:alpha_CCD}
$$
> Note that the problem definition implicitly requires \\(d_{jk}(x) > 0\\). Under this setting, if we denote \\(d^a_{jk}(\alpha) = d_{jk}(x + \alpha p)\\), \\(\alpha^C_{jk}\\) is simply the smallest positive real root of \\(d^a_{jk}(\alpha)\\) (see {{ref: fig:lec8:CCD}} for an example), or \\(\alpha^C_{jk} = \infty\\) if \\(d^a_{jk}(\alpha)\\) does not have any positive real roots. There are many methods to obtain the exact or a conservative estimate of \\(\alpha^C_{jk}\\), we will see a specific example in the case study of this lecture. After computing \\(\alpha^C_{jk}\\) for all nodes \\(j\\) and obstacle \\(k\\), a step size upper bound \\(\alpha^C\\) for the line search could then be obtained as
$$
    \alpha^C = \min(1, \min_{j,k} \alpha^C_{jk})
$$ <figure>
    <center>
    <img src="img/lec8/CCD.jpg" width="250">
    </center>
    <figcaption><b>{{fig}}{fig:lec8:CCD}</b> An illustration of CCD with a solid particle at $(0, 0)$ hitting a fixed vertical plane at $x=0.3$. With the intended displacement $\mathbf{p}=(0.5, 0)$, we obtain $\alpha^C = 0.6$. </figcaption>
</figure>

Now, we can introduce our filter line search method ({{ref: alg:lec8:filter_line_search}}), specifically designed to enforce non-interpenetration constraints throughout the entire approximated motion trajectory. This strategic enforcement is key in preventing tunneling issues that commonly occur in simulations with insufficient constraint handling.

This new scheme differs from the traditional backtracking line search method in a critical aspect: it initializes the step size. Instead of starting with a step size of \\(1\\), the filter line search method begins with \\(\alpha^C\\). This modification is subtle yet significant.


<figure>
    <center>
    <b><figcaption>{{alg}}{alg:lec8:filter_line_search}[Filter Backtracking Line Search]</figcaption></b>
    <img src="img/lec8/alg_filter_ls.jpg" width="610">
    </center>
</figure>

> **{{rem}}{rm:lec8:algorithm_dependency}[Algorithm Dependency Issue]**
> Using the optimization path to approximate the motion trajectory is still not perfect as it is algorithm dependent. Other than the projected Newton (PN) method, there could be an algorithm that walks around an obstacle and ended up with a configuration on the other side, still providing a tunneling solution ({{ref: fig:lec8:still_tunnel}}). 
> Even with projected Newton, although in practice it always generates straightforward and physically plausible trajectories, there is no theoretical guarantee that it will never encounter tunneling issues. 
> An intuition is that the search direction in every PN iteration always significantly decreases the Incremental Potential (IP), and so it is unlikely to walk around any contacts which often results in iterations that do not sufficiently decrease the IP.
> In fact, this kind of issue also happens in elastodynamics simulation without contact. Elasticity energy itself is also nonconvex, which can result in multiple local optima for the IP. The key to obtaining physical behaviors is to **locally minimize** IP, in other words, finding the nearby local minimum as the solution. <figure>
    <center>
    <img src="img/lec8/still_tunnel.jpg" width="500">
    </center>
    <figcaption><b>{{fig}}{fig:lec8:still_tunnel}</b> For the setup in the tunneling example, even with the filter line search scheme, if an optimization method other than projected Newton is applied, it could still lead to the tunneling issue. </figcaption>
</figure>
