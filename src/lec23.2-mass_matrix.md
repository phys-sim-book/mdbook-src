## Mass Matrix

Recall that the mass matrix can be calculated as
$$
M_{ab} = \sum_{e\in\mathcal{T}} \int_{\Omega^0_e} R(\mathbf{X},0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X},
$$
where $\Omega^0_e$ represents the material space of tetrahedron $e$. Changing the integration variable from $\mathbf{X}$ to $(\beta, \gamma, \tau)$ results in
$$
\begin{aligned}
    & \int_{\Omega^0_e} R(\mathbf{X},0) N_a(\mathbf{X}) N_b(\mathbf{X}) d\mathbf{X} \\
    = & \int_0^1 \int_0^{1-\tau} \int_0^{1-\beta-\tau} R(\beta, \gamma, \tau, 0) N_a(\beta, \gamma, \tau) N_b(\beta, \gamma, \tau) \left| \det\left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma, \tau)}\right) \right| d\gamma d\beta d\tau.
\end{aligned}
$$

For element $e$ with vertices $\mathbf{X}_1$, $\mathbf{X}_2$, $\mathbf{X}_3$, and $\mathbf{X}_4$,
$$
\left| \det\left(\frac{\partial \mathbf{X}}{\partial (\beta, \gamma, \tau)}\right) \right| = \left| \det([\mathbf{X}_2 - \mathbf{X}_1, \mathbf{X}_3 - \mathbf{X}_1, \mathbf{X}_4 - \mathbf{X}_1]) \right| = 6 V_e,
$$
where $V_e$ is the volume of tetrahedron $e$. 

Here, we will omit the detailed derivations of each entry in the consistent mass matrix. Assuming uniform density $R$, for the lumped mass matrix,
$$
M_{aa}^\text{lump} = \sum_{e\in\mathcal{T}(a)} \frac{1}{4} R V_e \quad \text{and} \quad M_{ab}^\text{lump} = 0 \quad (a \neq b),
$$
where $\mathcal{T}(a)$ denotes the set of tetrahedra incident to node $a$. In other words, the mass of each tetrahedron is evenly distributed among its 4 nodes, which is intuitively analogous to the 2D case.
