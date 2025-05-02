## Drucker-Prager Elastoplasticity

The **Drucker-Prager** plasticity model is widely used for simulating granular materials such as sand and soil. It generalizes the von Mises model by incorporating a **friction angle**, which governs how much shear stress the material can sustain relative to the normal stress. Physically, this corresponds to Coulomb-like friction between particles: the material yields when the shear stress exceeds a friction-dependent bound based on pressure.

In stress space, the Drucker-Prager yield surface takes the shape of a **cone**, with three distinct cases to handle:
- **Case I (Elastic)**: The stress lies strictly inside the cone, and no plasticity occurs.
- **Case II (Expansion)**: The stress corresponds to a configuration where the material expands volumetrically (positive trace), and no resistance is applied—this maps to the cone tip.
- **Case III (Shearing)**: The stress lies outside the cone but with compressive pressure and must be projected back to the cone surface.

This model is best implemented in the **log-strain (Hencky strain)** space using the SVD of the deformation gradient:
Let $\mathbf{F}_\mathrm{tr} = \mathbf{U} \boldsymbol{\Sigma} \mathbf{V}^T$ be the trial deformation gradient, which can be obtained from Hencky strain-based Saint Venant–Kirchhoff model (StVK) constitutive model:

```python
{{#include solid-sim-tutorial/11_mpm_sand/simulator.py:stvk}}
```

> {{exp}}{exp:lec27:drucker_prager_yield_criterion}[Drucker-Prager Yield Criterion, Log-Strain Formulation]
> We define the **logarithmic strain** as:
>
> $$
\boldsymbol{\epsilon} = \log(\boldsymbol{\Sigma}) + \frac{1}{d} \log(J_\mathrm{vol}) \, \mathbf{I}
$$
>
> The deviatoric part is:
>
> $$
\hat{\boldsymbol{\epsilon}} = \boldsymbol{\epsilon} - \frac{1}{d} \, \text{tr}(\boldsymbol{\epsilon}) \, \mathbf{I}
$$
>
> The plastic multiplier is computed as:
>
> $$
\Delta \gamma = \|\hat{\boldsymbol{\epsilon}}\| + \frac{(d\lambda + 2\mu)}{2\mu} \cdot \text{tr}(\boldsymbol{\epsilon}) \cdot \alpha
$$
>
> Here, $\alpha = \sqrt{\frac{2}{3}} \cdot \frac{2\sin\phi}{3 - \sin\phi}$ is the Drucker-Prager friction coefficient derived from the friction angle $\phi$.
>
> Then we apply the return mapping:
> 
> - If $\text{tr}(\boldsymbol{\epsilon}) > 0$ (Case II), we project to the cone tip: set $\boldsymbol{\epsilon} = 0$.
> - If $\Delta \gamma \le 0$, we are inside the cone (Case I): no change.
> - Otherwise (Case III), we project back to the cone surface:
>
> $$
\boldsymbol{\epsilon}^{n+1} = \boldsymbol{\epsilon} - \frac{\Delta \gamma}{\|\hat{\boldsymbol{\epsilon}}\|} \, \hat{\boldsymbol{\epsilon}}
$$
>
> Finally, we compute the updated singular values:
>
> $$
\boldsymbol{\Sigma}_\mathrm{E}^{n+1} = \exp(\boldsymbol{\epsilon}^{n+1})
$$
>
> and reconstruct the elastic deformation:
>
> $$
\mathbf{F}_\mathrm{E}^{n+1} = \mathbf{U} \, \text{diag}(\boldsymbol{\Sigma}_\mathrm{E}^{n+1}) \, \mathbf{V}^T
$$

> {{exp}}{exp:lec29:drucker_prager_projection}[Drucker-Prager Plasticity with Volume Correction]
>
> In granular materials like sand, volumetric expansion can result in **non-physical volume gain** if not properly handled. The standard Drucker-Prager projection maps stress to the tip of the cone under expansion (positive trace), which corresponds to a **stress-free state**. However, this may unrealistically "lock in" expanded configurations as new rest shapes.
>
> This effect can cause persistent volume inflation when a particle experiences elastic expansion followed by plastic projection. Any future compression then incurs artificial elastic penalties, resulting in incorrect material response.
>
> To correct this, we follow the volume correction treatment described by @@tampubolon2017multi by introducing a per-particle scalar accumulator $v_\mathrm{vol}$ that tracks **log-volume changes** induced by plastic projection:
>
> $$
v_\mathrm{vol}^{n+1} = v_\mathrm{vol}^n - \log \det \mathbf{F}_\mathrm{E}^{n+1} + \log \det \mathbf{F}_\mathrm{trial}
$$
>
> This correction is naturally integrated into the **log-strain formulation** by adjusting the strain before return mapping:
>
> $$
\boldsymbol{\epsilon}_\text{corrected} = \boldsymbol{\epsilon}_\text{trial} + \frac{v_\mathrm{vol}}{d} \mathbf{I}
$$
>
> where $d$ is the spatial dimension. This allows future compression to **neutralize previous volume gain** rather than being resisted elastically. In the code below, `diff_log_J` provides this volume correction term, computed as the accumulation of log-difference of determinants.

```python
{{#include solid-sim-tutorial/11_mpm_sand/simulator.py:drucker_prager}}
```