## Friction Energy and Its Derivatives

With $\lambda$, $r$, and $\mathbf{n}$ precomputed for each friction point-edge pair, we can now conveniently compute the energy ({{ref: imp:lec22:fric_val}}), gradient ({{ref: imp:lec22:fric_grad}}), and Hessian ({{ref: imp:lec22:fric_hess}}) of the friction potential and add them into the optimization.

{{imp}}{imp:lec22:fric_val}[Friction energy value, FrictionEnergy.py]
```python
{{#include solid-sim-tutorial/8_self_friction/FrictionEnergy.py:val}}
```

When computing the gradient and Hessian, we used the relative velocity $\hat{\mathbf{v}}_k$ as an intermediate variable to make the implementation more organized. This approach is given by:
$$
\nabla P_f(x) = \sum_k \bigg(\frac{\partial \hat{\mathbf{v}}_k}{\partial x}\bigg)^T \frac{\partial D_k(x)}{\partial \hat{\mathbf{v}}_k}, \quad 
\nabla^2 P_f(x) = \sum_k \bigg(\frac{\partial \hat{\mathbf{v}}_k}{\partial x}\bigg)^T \frac{\partial^2 D_k(x)}{\partial \hat{\mathbf{v}}_k^2} \frac{\partial \hat{\mathbf{v}}_k}{\partial x},
$$
where the derivatives of $D_k$ with respect to $\hat{\mathbf{v}}_k$ have exactly the same forms as in [Frictional Contact](./lec9-friction.md).


{{imp}}{imp:lec22:fric_grad}[Friction energy gradient, FrictionEnergy.py]
```python
{{#include solid-sim-tutorial/8_self_friction/FrictionEnergy.py:grad}}
```

{{imp}}{imp:lec22:fric_hess}[Friction energy Hessian, FrictionEnergy.py]
```python
{{#include solid-sim-tutorial/8_self_friction/FrictionEnergy.py:hess}}
```

After these implementations, we can finally run our compressing squares example with frictional self-contact (see: {{ref: fig:lec22:compress_2_squares_fric}}). From the figure, we observe that once the two squares touch, the large friction between them and the ground restricts any sliding. This causes the squares to rotate counter-clockwise as they are compressed by the ceiling.


<figure>
    <center>
    <img src="img/lec22/compress_2_squares_fric.jpg">
    </center>
    <figcaption><b>{{fig}}{fig:lec22:compress_2_squares_fric}</b> Two squares dropped onto the ground and compressed by a ceiling. The friction coefficient is $0.4$ between any contacting surfaces, which restricts any sliding here in this scene and results in counter-clockwise rotations of the two squares under compression. As their interface becomes nearly vertical, the squares are finally detached.</figcaption>
</figure>
