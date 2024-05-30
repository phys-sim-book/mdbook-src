## Line Search

In iterative minimization methods, line search is a technique used to select a fraction of the step in each iteration, ensuring the objective energy decreases at the new point.

Specifically, for Newton's method, line 4 in {{ref: alg:lec1:newton_method_IE}} is modified from \\(x^i \leftarrow x\\) to \\(x^i \leftarrow x^i + \alpha (x - x^i)\\), where \\(\alpha \in (0,1]\\) is the step size, essential for the reduction of energy. This leads to two critical questions: Does such an \\(\alpha\\) always exist? And how is \\(\alpha\\) calculated?

> **{{rem}}{rem:lec3:line_search_existence}[Existence of \\(\alpha\\)]** For a smooth objective energy \\(E(x)\\) at \\(x^i\\) where \\(\nabla E(x^i) \neq 0\\), if a search direction \\(p=x-x^i\\) is descent, namely \\(p^T \nabla E(x^i) < 0\\), then there exists \\(\alpha > 0\\) such that \\(E(x^i + \alpha p) < E(x^i)\\).

> **{{met}}{met:lec3:backtracking}[Backtracking Line Search]** Given a descent direction, we can find a reasonably large \\(\alpha\\) by simply halving it starting from \\(1\\) until the energy at the new location is smaller than the current (see {{ref: alg:lec3:line_search}}). <figure>
    <center>
    <b><figcaption>{{alg}}{alg:lec3:line_search}[The Backtracking Line Search Algorithm]</figcaption></b>
    <img src="img/lec3/line_search_algorithm.jpg" width="650">
    </center>
</figure>

> **{{rem}}{rem:lec3:other_line_search}[Other Line Search Methods]**
There are other line search methods that attempt to apply polynomial interpolations to find an \\(\alpha\\) such that the energy at the new location is closer to a local minimum on the line segment \\(x^i + s p\\), (\\(s\in(0,1]\\)). However, these methods generally incur higher computational costs and may not necessarily enhance the overall wall-clock timing of the optimization.

Now, with line search, if Newton's method consistently generates a descent search direction, then the method is guaranteed to converge for any initial configuration on any smooth energy with a lower bound. We know that in iteration \\(i\\), \\(p = -(\nabla^2 E(x^i))^{-1} \nabla E(x^i)\\), so \\(p^T \nabla E(x^i)\\) equals \\(-\nabla E(x^i)^T (\nabla^2 E(x^i))^{-T} \nabla E(x^i)\\). For convex energies, \\(\nabla^2 E(x^i)\\) is always Symmetric Positive Definite (SPD), and so is \\((\nabla^2 E(x^i))^{-T}\\), making \\(p\\) always a descent direction. However, for non-convex energies, this assurance does not always hold. One approach to address this issue is to approximate the energies locally using convex energy proxies.
