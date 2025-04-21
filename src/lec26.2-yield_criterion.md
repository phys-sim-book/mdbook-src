## Yield Condition and Return Mapping

To enforce yield condition and flow rule in discrete time and space, we apply a procedure known as **return mapping**. This is a projection-based algorithm that modifies the elastic deformation gradient such that the resulting stress satisfies the yield condition.

### Return Mapping

Let $\mathbf{F}_\mathrm{tr}$ denote the **trial elastic deformation gradient**, obtained after the [deformation update](src/lec25.4-particle_state_update.md) but before plasticity is enforced. If the stress computed from $\mathbf{F}_\mathrm{tr}$ lies **inside** the yield surface, no correction is needed as the deformation is purely elastic.

If the stress lies **outside** the yield surface, we apply return mapping to correct the trial elastic state, projecting the stress induced by $\mathbf{F}_{\mathrm{tr}}$ back onto the yield surface, following the direction specified by the plastic flow rule. We denote the result of the return mapping as:

$$
\mathbf{F}_\mathrm{E}^{n+1} = \mathcal{Z}(\mathbf{F}_\mathrm{tr})
$$

where $\mathcal{Z}(\cdot)$ is the **return mapping operator** that corrects the elastic predictor based on the yield surface geometry and material model.

In practice, return mapping is implemented either analytically (for simple yield criteria such as von Mises or Drucker-Prager) or iteratively via numerical solvers when the yield surface is complex.

> {{exp}}{exp:lec26:von_mises_log_strain}[von Mises Yield Criterion, Log-Strain Formulation]
>
> In log-strain-based plasticity, return mapping is performed in the diagonalized SVD space. Let $\mathbf{F}^{n+1} = \mathbf{U} \boldsymbol{\Sigma} \mathbf{V}^T$ and let $\boldsymbol{\sigma}_p^n$ be the stored plastic log-strain. The trial elastic stretch is:
> $$
\boldsymbol{\Sigma}_\mathrm{tr} = \boldsymbol{\Sigma} \cdot \left( \exp(\boldsymbol{\sigma}_p^n) \right)^{-1}, \quad
\boldsymbol{\epsilon}_\mathrm{tr} = \log(\boldsymbol{\Sigma}_\mathrm{tr})
$$
> Compute the deviatoric log strain:
> $$
\boldsymbol{\epsilon}_\mathrm{dev} = \boldsymbol{\epsilon}_\mathrm{tr} - \frac{1}{d} \, \text{tr}(\boldsymbol{\epsilon}_\mathrm{tr}) \, \mathbf{I}
$$
> If $\| \boldsymbol{\epsilon}_\mathrm{dev} \| > \sqrt{2/3} \, \epsilon_Y$, perform radial return:
> $$
\boldsymbol{\epsilon}_\mathrm{dev}^{n+1} = \left( 1 - \frac{ \| \boldsymbol{\epsilon}_\mathrm{dev} \| - \sqrt{2/3} \epsilon_Y }{ \| \boldsymbol{\epsilon}_\mathrm{dev} \| } \right) \boldsymbol{\epsilon}_\mathrm{dev}
$$
> The corrected elastic log strain is:
> $$
\boldsymbol{\epsilon}_\mathrm{log}^{n+1} = \boldsymbol{\epsilon}_\mathrm{dev}^{n+1} + \frac{1}{d} \, \text{tr}(\boldsymbol{\epsilon}_\mathrm{tr}) \, \mathbf{I}
$$
> Then update:
> $$
\boldsymbol{\Sigma}_\mathrm{E}^{n+1} = \exp(\boldsymbol{\epsilon}_\mathrm{log}^{n+1}), \quad
\boldsymbol{\sigma}_p^{n+1} = \log(\boldsymbol{\Sigma}) - \log(\boldsymbol{\Sigma}_\mathrm{E}^{n+1})
$$
> and reconstruct the elastic deformation:
> $$
\mathbf{F}_\mathrm{E}^{n+1} = \mathbf{U} \boldsymbol{\Sigma}_\mathrm{E}^{n+1} \mathbf{V}^T
$$