## Computing $\mathbf{P}$

Let's begin with the computation of $\mathbf{P}$. For isotropic materials, the first Piola-Kirchhoff stress tensor can be calculated as follows:
$$
\begin{aligned}
    \mathbf{P} & = \mathbf{U} \hat{\mathbf{P}} \mathbf{V}^T \\
    \text{where} \quad
    \mathbf{F} & = \mathbf{U} \bm{\Sigma} \mathbf{V}^T, \quad
    \Psi(\mathbf{F}) = \hat{\Psi}(\bm{\Sigma}), \quad \text{and} \quad
    \hat{P}_{ij} = \frac{\partial \hat{\Psi}}{\partial \sigma_i} \delta_{ij}.
\end{aligned}
{{numeq}}{eqn:PdiagonalSpace}
$$
This formulation leverages the property that $\mathbf{P}$ shares the same SVD space as $\mathbf{F}$, which simplifies the derivation and computation process.

> **{{exp}}{exp:lec13:neo_hook_model}**
> For the Neo-Hookean model (Equation {{eqref: eq:lec13:strain_density_NH}}), we have:
$$
    \hat{\Psi}_{\text{NH}}(\bm{\Sigma}) = \frac{\mu}{2} (\sum_i^d \sigma_{i}^2 - d) - \mu \ln(J) + \frac{\lambda}{2} \ln^2(J).
$$
> Thus, we can first perform SVD on $\mathbf{F} = \mathbf{U} \bm{\Sigma} \mathbf{V}$ and derive:
$$
    \hat{P}_{ii} = \mu (\sigma_i - \frac{1}{\sigma_i}) + \lambda \ln(J) \frac{1}{\sigma_i}
$$
> to compute $\frac{\partial \Psi}{\partial \mathbf{F}} = \mathbf{P} = \mathbf{U} \hat{\mathbf{P}} \mathbf{V}^T$ without symbolically deriving the derivative of $\Psi$ w.r.t. $\mathbf{F}$.

Here we provide the proof that $\mathbf{P}$ commutes with rotations in diagonal space (see Equation {{eqref: eqn:PdiagonalSpace}}). To demonstrate that $\mathbf{P}(\mathbf{R}\mathbf{F}) = \mathbf{R}\mathbf{P}(\mathbf{F})$ for any rotation matrix $\mathbf{R}$, consider a generic (potentially anisotropic) material model. The key idea is that a rotation applied after deformation does not alter the material's stored energy, thus we have the identity $\Psi(\mathbf{F}) = \Psi(\mathbf{R}\mathbf{F})$. Differentiating both sides of this equation with respect to the deformation gradient $\mathbf{F}$ yields:

$$
\begin{align*}
\delta \Psi &= \frac{\partial \Psi}{\partial \mathbf{F}}(\mathbf{F}) : \delta \mathbf{F} = \frac{\partial \Psi}{\partial \mathbf{F}}(\mathbf{R}\mathbf{F}) : \delta (\mathbf{R}\mathbf{F}), \\
\mathbf{P}(\mathbf{F}) : \delta \mathbf{F} &= \mathbf{P}(\mathbf{R}\mathbf{F}) : (\mathbf{R} \delta \mathbf{F}), \\
\mathbf{P}(\mathbf{F}) : \delta \mathbf{F} &= (\mathbf{R}^T \mathbf{P}(\mathbf{R}\mathbf{F})) : \delta \mathbf{F}, \\
\mathbf{P}(\mathbf{F}) &= \mathbf{R}^T \mathbf{P}(\mathbf{R}\mathbf{F}), \\
\mathbf{R} \mathbf{P}(\mathbf{F}) &= \mathbf{P}(\mathbf{R}\mathbf{F}).
\end{align*}
$$

Furthermore, for an isotropic material where $\Psi(\mathbf{F}\mathbf{R}) = \Psi(\mathbf{F})$, a similar argument shows that $\mathbf{P}(\mathbf{F}\mathbf{R}) = \mathbf{P}(\mathbf{F})\mathbf{R}$. Combining these relationships for $\mathbf{P}$ under rotation, we establish that:
$$
\mathbf{P}(\mathbf{F}) = \mathbf{P}(\mathbf{U}\bm{\Sigma}\mathbf{V}^T) = \mathbf{U}\mathbf{P}(\bm{\Sigma})\mathbf{V}^T = \mathbf{U}\hat{\mathbf{P}}\mathbf{V}^T.
$$
This formulation confirms the rotational invariance of $\mathbf{P}$ in diagonal space.

### Additional Proof for $\mathbf{P}(\bm{\Sigma})=\hat{\mathbf{P}}=\frac{\partial\hat{\Psi}}{\partial \Sigma}$

In the above, the last equality comes from the fact that 
$$
\mathbf{P}(\mathbf{F}=\bm{\Sigma}) = \frac{\partial\hat{\Psi}}{\partial\bm{\Sigma}}.
$$
Here we show why this is true.

(1) First, we claim that $\mathbf{P}(\bm{\Sigma})$ is diagonal. This can be seen by realizing that for isotropic elasticity,
$$
\mathbf{P}(\mathbf{F}) = \sum_k \frac{\partial \Psi}{\partial I_k}(\mathbf{F}) \frac{\partial I_k}{\partial \mathbf{F}}(\mathbf{F}),
$$
where $I_k$ is the isotropic invariants. Following {{#cite sifakis2022finite}} (page 23), we can observe that $\frac{\partial I_k}{\partial \mathbf{F}}(\mathbf{F})$ when the argument $\mathbf{F}$ is diagonal, must be diagonal. Therefore, $\mathbf{P}(\mathbf{F})$ is diagonal when $\mathbf{F}$ is diagonal. 

(2) Next, we claim that
$$
\text{diag}(\frac{\partial \Sigma}{\partial F_{ij}}) = \text{diag}(\mathbf{U}^T \frac{\partial \mathbf{F}}{\partial F_{ij}} \mathbf{F}).
$$
This is proven in {{#cite xu2015nonlinear}} (Equation 7).


(3) Based on (2), we know that for any $ij$, after substituting $\mathbf{F}=\bm{\Sigma}$, we have
$$
\text{diag}(\frac{\partial \Sigma}{\partial F_{ij}}(\Sigma)) = \text{diag}(\mathbf{I}^T \frac{\partial \mathbf{F}}{\partial F_{ij}}(\Sigma) \mathbf{I}),
$$
using this we can write out the cases for $ij=11, ij=22,ij=33$. For example, for $ij=11$, we have
$$
\frac{\partial \Sigma}{\partial F_{11}}(\Sigma) = \begin{pmatrix}
1 & * & * \\
* & 0 & *\\
* & * & 0 
\end{pmatrix}
$$

(4) Finally, let's derive $\mathbf{P}(\Sigma)$. Since we know it is diagonal from (1), we just need to derive its diagonal entry. Let's use $11$ entry as an example:
$$
\begin{align*}
P_{ab}(\Sigma) &= \frac{\partial \hat{\Psi}}{\partial F_{ab}}(\Sigma)= \frac{\partial \hat{\Psi}}{\partial \Sigma}(\Sigma) : \frac{\partial\Sigma}{\partial F_{ab}}(\Sigma)\\
P_{11}(\Sigma) &= \frac{\partial \hat{\Psi}}{\partial \Sigma}(\Sigma) : \frac{\partial\Sigma}{\partial F_{11}}(\Sigma)\\
P_{11}(\Sigma) &= \begin{pmatrix}
\frac{\partial\hat{\Psi}}{\partial \sigma_1} &&\\
&\frac{\partial\hat{\Psi}}{\partial \sigma_1} &\\
&&\frac{\partial\hat{\Psi}}{\partial \sigma_1} \\
\end{pmatrix}
:
\begin{pmatrix}
1 & * & * \\
* & 0 & *\\
* & * & 0 
\end{pmatrix}
=\frac{\partial\hat{\Psi}}{\partial \sigma_1}
\end{align*}
$$
Now are are done with the final proof.