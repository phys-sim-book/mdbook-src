## Mass Matrix and Lumping

Recall from [Discretization of Weak Forms](./lec17-disc_weak_form.md) that:

$$
M_{ab} = \int_{\Omega^0} R(\mathbf{X},0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X}.
{{numeq}}{eq:lec19:mass_matrix}
$$

With the solid domain discretized into triangles $\mathcal{T}$, we have:

$$
M_{ab} = \sum_{e \in \mathcal{T}} \int_{\Omega^0_e} R(\mathbf{X},0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X},
{{numeq}}{eq:lec19:mass_matrix_sum_tri}
$$

where $\Omega^0_e$ represents the material space of triangle $e$. Note that for linear triangle elements, since $N_i$ is nonzero only on the incident triangles of node $i$, here we only need to consider triangles with both $a$ and $b$ being their vertices.

Let us change the integration variable from $\mathbf{X}$ to $(\beta, \gamma)$, which gives:

$$
\begin{aligned}
& \int_{\Omega^0_e} R(\mathbf{X},0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X} \\
=& \int_0^1 \int_0^{1-\beta} R(\beta, \gamma, 0) N_a(\beta, \gamma) N_b(\beta, \gamma) \left|\det\left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma)}\right)\right| d\gamma d\beta.
\end{aligned}
{{numeq}}{eq:lec19:mass_matrix_per_tri_param}
$$

For simplicity, let us denote the vertices of this triangle $e$ as $\mathbf{X}_1$, $\mathbf{X}_2$, and $\mathbf{X}_3$, and then we have:

$$
\left|\det\left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma)}\right)\right| = |\det([\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1])| = 2 A_e,
$$

where $A_e$ is the area of triangle $e$. Here, $N_a$ and $N_b$ take $1-\beta-\gamma$, $\beta$, or $\gamma$ depending on the vertex indices $a$ and $b$. For example, if $a$ and $b$ correspond to the 2nd and 3rd vertices of triangle $e$, then $N_a = \beta$ and $N_b = \gamma$. Assuming uniform density, we have:

$$
\begin{aligned}
    \int_{\Omega^0_e} R(\mathbf{X},0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X}
    = & 2 R A_e \int_0^1 \int_0^{1-\beta} \beta \gamma d\gamma d\beta \\
    = & 2 R A_e \int_0^1 \left. \frac{1}{2} \beta \gamma^2 \right|_{\gamma=0}^{\gamma=1-\beta} d\beta \\
    =& R A_e \int_0^1 \beta (1-\beta)^2 d\beta \\
    = & R A_e \left. \left(\frac{\beta^2}{2} - \frac{2\beta^3}{3} + \frac{\beta^4}{4}\right) \right|_{\beta=0}^{\beta=1} = \frac{1}{12} R A_e.
\end{aligned}
{{numeq}}{eq:lec19:mass_matrix_per_tri_betagamma}
$$

With mass lumping, $M^{\text{lump}}_{ab} = \delta_{ab}\sum_c M_{ac}$, which means:

$$
M_{aa}^\text{lump} = \sum_{e \in \mathcal{T}} \sum_{b \in \mathcal{V}} \int_{\Omega^0_e}  R(\mathbf{X},0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X},
{{numeq}}{eq:lec19:lumped_mass_matrix_sum_tri}
$$

where $\mathcal{V}$ contains all the nodes of the mesh, and all off-diagonal entries of $M^\text{lump}$ are $0$. Similarly, due to the locality of $N$, for each triangle element, $b$ only needs to traverse all three triangle vertices:

$$
\begin{aligned}
    M_{aa}^\text{lump} & = \sum_{e \in \mathcal{T}(a)} 2 R A_e \left( \int_0^1 \int_0^{1-\beta} \beta (1- \beta - \gamma) d\gamma d\beta + \int_0^1 \int_0^{1-\beta} \beta^2 d\gamma d\beta\right. \\ &+ \left.\int_0^1 \int_0^{1-\beta} \beta \gamma d\gamma d\beta \right) \\
    & = \sum_{e \in \mathcal{T}(a)} 2 R A_e \int_0^1 \beta d\gamma d\beta = \sum_{e \in \mathcal{T}(a)} 2 R A_e \int_0^1 \beta \gamma |_{\gamma=0}^{\gamma =1-\beta} d\beta \\
    & = \sum_{e \in \mathcal{T}(a)} 2 R A_e \int_0^1 \beta (1-\beta) d\beta = \sum_{e \in \mathcal{T}(a)} 2 R A_e \left. \left(\frac{\beta^2}{2} - \frac{\beta^3}{3} \right) \right|_{\beta=0}^{\beta=1} \\ 
    &= \sum_{e \in \mathcal{T}(a)} \frac{1}{3} R A_e,
\end{aligned}
{{numeq}}{eq:lec19:lumped_mass_matrix_sum_tri_param}
$$

where $\mathcal{T}(a)$ denotes the set of triangles incident to node $a$. This result also explains why in [Inversion-Free Elasticity](./lec15-inv_free_elasticity.md) when computing the mass for all the nodes, we traverse all triangles, calculate the mass of the triangle $RA_e$ and evenly distribute it to the three vertices. With the mass matrix computed, the momentum change and external body force terms including their energy forms are all easy to deal with.
