## Computing Energy, Gradient, and Hessian

We first follow sections [Strain Energy](lec13-strain_energy.md) and [Stress and Its Derivatives](lec14-stress_and_derivatives.md) to implement computing $\Psi(\mathbf{F})$, $\mathbf{P} = \frac{\partial \Psi}{\partial \mathbf{F}}$, and SPD-projected $\frac{\partial \mathbf{P}}{\partial \mathbf{F}}$:

{{imp}}{imp:lec15:NeoHookeanEnergy_wrt_F}[Energy derivatives w.r.t. $\mathbf{F}$, NeoHookeanEnergy.py]
```python
{{#include solid-sim-tutorial/6_inv_free/NeoHookeanEnergy.py:helper_func}}
```

Next, we implement computing $\mathbf{F}(x)$, and the tensor products with $\frac{\partial \mathbf{F}}{\partial x}$ for chain rule based computation of elasticity energy gradient and Hessian:

{{imp}}{imp:lec15:NeoHookeanEnergy_F_x}[Energy derivatives w.r.t. $x$, NeoHookeanEnergy.py]
```python
{{#include solid-sim-tutorial/6_inv_free/NeoHookeanEnergy.py:stress_deriv}}
```

Finally, Neo-Hookean energy value, gradient, and Hessian on the entire mesh can be computed as follows:

{{imp}}{imp:lec15:NeoHookeanEnergy_wrt_x}[Energy value, Gradient, and Hessian, NeoHookeanEnergy.py]
```python
{{#include solid-sim-tutorial/6_inv_free/NeoHookeanEnergy.py:val_grad_hess}}
```
