## Barrier Energy and Its Derivatives

With the point-edge distance functions implemented, we can traverse all point-edge pairs to assemble the total barrier energy and its derivatives. These will be used to solve for the search direction in the time-stepping optimization.

Since squared distances are used, here we rescale the barrier function to
$$
b(d^2, \hat{d}^2) = 
\begin{cases}
    \frac{\kappa}{8} \hat{d} \left(\frac{d^2}{\hat{d}^2} - 1\right) \ln \frac{d^2}{\hat{d}^2} & d < \hat{d} \\
    0 & d \geq \hat{d},
\end{cases}
{{numeq}}{eq:lec21:scaled_barrier_func}
$$
so that 
$$
\frac{\partial^2 b}{\partial (\frac{d}{\hat{d}})^2}(\hat{d}^2, \hat{d}^2) = \kappa \hat{d}
$$
still holds. Analogous to elasticity, $s = d/\hat{d}$ can be viewed as a strain measure, then the 2nd-order derivative of the energy density (per area) function $b$ w.r.t. $s$ at $s=1$ would correspond to Young's modulus times thickness $\hat{d}$, which makes $\kappa$ physically meaningful and convenient to set.

Based on Equation {{eqref: eq:lec20:barrier_discrete}}, we can derive the gradient and Hessian of the barrier potential as
$$
\begin{aligned}
    \nabla P_b(x) & = \frac{1}{2} \sum_{\hat{a}} A_{\hat{a}}  \sum_{e \in \mathcal{E} - I(\mathbf{X}_{\hat{a}})} \frac{\partial b}{\partial d}( d(\mathbf{x}_{\hat{a}}, e), \hat{d}^2) \nabla_x d(\mathbf{x}_{\hat{a}}, e) \ \text{and} \\
    \nabla^2 P_b(x) & = \frac{1}{2} \sum_{\hat{a}} A_{\hat{a}}  \sum_{e \in \mathcal{E} - I(\mathbf{X}_{\hat{a}})} \Big( \frac{\partial^2 b}{\partial d^2}( d(\mathbf{x}_{\hat{a}}, e), \hat{d}^2) \nabla_x d(\mathbf{x}_{\hat{a}}, e) \nabla_x d(\mathbf{x}_{\hat{a}}, e)^T + \\&\frac{\partial b}{\partial d}( d(\mathbf{x}_{\hat{a}}, e), \hat{d}^2) \nabla^2_x d(\mathbf{x}_{\hat{a}}, e) \Big),
\end{aligned}
$$
where $A_{\hat{a}} = \frac{\|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} - 1} \| + \|\mathbf{X}_{\hat{a}} - \mathbf{X}_{\hat{a} + 1} \|}{2}$ and we omitted the superscripts and subscripts for the squared point-edge distance functions ($d(\mathbf{x}_{\hat{a}}, e)$ denotes $d^{\text{PE}}_{\text{sq}}(\mathbf{x}_{\hat{a}}, e)$ here).

The energy, gradient, and Hessian of the barrier contact potential are implemented as follows:

{{imp}}{imp:lec21:BarrierEnergy_value}[Barrier energy computation, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/7_self_contact/BarrierEnergy.py:value}}
```

{{imp}}{imp:lec21:BarrierEnergy_grad}[Barrier energy gradient computation, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/7_self_contact/BarrierEnergy.py:gradient}}
```

{{imp}}{imp:lec21:BarrierEnergy_hess}[Barrier energy Hessian computation, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/7_self_contact/BarrierEnergy.py:Hessian}}
```
