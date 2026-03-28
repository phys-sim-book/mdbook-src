## Continuous Collision Detection

Now, we have all the ingredients to solve for the search direction in a simulation with self-contact. After obtaining the search direction, we perform line search filtering for the point-edge pairs.

{{imp}}{imp:lec21:BarrierEnergy_CCD}[Line search filtering, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/7_self_contact/BarrierEnergy.py:line_search_filtering}}
```

Here, we perform an overlap check on the bounding boxes of the spans of the point and edge first to narrow down the number of point-edge pairs for which we need to compute the time of impact:

{{imp}}{imp:lec21:CCD_broad}[Bounding box overlap check, CCD.py]
```python
{{#include solid-sim-tutorial/7_self_contact/distance/CCD.py:broad_phase}}
```

To calculate a sufficiently large conservative estimation of the time of impact (TOI), we cannot directly calculate the TOI and take a proportion of it as we did for point-ground contact in [Filter Line Search](./lec8-filter_line_search.md). Directly calculating the TOI for contact primitive pairs requires solving quadratic or cubic root-finding problems in 2D and 3D, which are prone to numerical errors, especially when distances are tiny and configurations are numerically degenerate (e.g., nearly parallel edge-edge pairs in 3D).

Thus, we implement the additive CCD method (ACCD) {{#cite li2021codimensional}}, which iteratively moves the contact pairs along the search direction until the minimum separation distance is reached, to robustly estimate the TOI.

Taking a point-edge pair as an example, the key insight of ACCD is that, given the current positions $\mathbf{p}$, $\mathbf{e}_0$, $\mathbf{e}_1$ and search directions $\mathbf{d}_p$, $\mathbf{d}_{e0}$, $\mathbf{d}_{e1}$, its TOI can be calculated as

$$
\alpha_\text{TOI} = \frac{\| \mathbf{p} - ((1-\lambda)\mathbf{e}_{0} + \lambda \mathbf{e}_{1}) \|}{\| \mathbf{d}_p - ((1-\lambda)\mathbf{d}_{e0} + \lambda \mathbf{d}_{e1}) \|}
$$

assuming $(1-\lambda)\mathbf{e}_0 + \lambda \mathbf{e}_1$ is the point on the edge that $\mathbf{p}$ will first collide with.
The issue is that we do not know $\lambda$ a priori. However, we can derive a lower bound for $\alpha_\text{TOI}$ as

$$
\begin{align}
    \alpha_\text{TOI} &\geq 
    \frac{\min_{\lambda\in[0,1]} \| \mathbf{p} - ((1-\lambda)\mathbf{e}_{0} + \lambda \mathbf{e}_{1}) \|}{ \| \mathbf{d}_p \| + \| (1-\lambda)\mathbf{d}_{e0} + \lambda \mathbf{d}_{e1} \|} \nonumber\\ 
    &\geq 
    \frac{d^\text{PE}(\mathbf{p}, \mathbf{e}_0, \mathbf{e}_1)}{ \| \mathbf{d}_p \| + \max(\| \mathbf{d}_{e0} \|,  \| \mathbf{d}_{e1} \|)}
    = \alpha_l
\end{align}
$$

By taking a step with this lower bound $\alpha_l$, we are guaranteed to have no interpenetration for this pair. However, although straightforward to compute, $\alpha_l$ can be much smaller than $\alpha_\text{TOI}$. Therefore, we iteratively calculate $\alpha_l$ and advance a copy of the participating nodes by this amount, accumulating all $\alpha_l$ to monotonically improve the estimate of $\alpha_\text{TOI}$ until the point-edge pair reaches a distance smaller than the minimum separation, e.g., $0.1\times$ the original distance. The implementation is as follows, where we first remove the shared components of the search directions so that they have smaller magnitudes to achieve earlier termination of the algorithm.

{{imp}}{imp:lec21:CCD}[ACCD method implementation, CCD.py]
```python
{{#include solid-sim-tutorial/7_self_contact/distance/CCD.py:accd}}
```

The final simulation results are demonstrated in {{ref: fig:lec21:compress_2_squares}}.

<figure>
    <center>
    <img src="img/lec21/compress_2_squares.jpg">
    </center>
    <figcaption><b>{{fig}}{fig:lec21:compress_2_squares}</b> Two squares dropped onto the ground and compressed by a ceiling. The ground has friction coefficient $0.4$ but there is no friction between the squares so that the top square slides down to the ground without significantly changing the position of the bottom one. </figcaption>
</figure>
