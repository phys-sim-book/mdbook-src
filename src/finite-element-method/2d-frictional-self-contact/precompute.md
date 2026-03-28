## Precomputing Normal and Tangent Information

To make the temporally discretized friction force integrable, we must explicitly discretize certain normal and tangent information. This information only needs to be calculated once at the beginning of each time step, as it will remain constant during each optimization.

First, we need to calculate $\lambda^{n}$ for each point-edge pair using $\mathbf{x}^{n}$. Recall that we used squared distances as input for the barrier functions, so $\lambda^{n}$ should be calculated using the chain rule as follows:

$$
\begin{aligned}
    \lambda^{n}_{\hat{a}, e} 
    & = \frac{1}{2}A_{\hat{a}} \left(-\frac{\partial b( d^\text{PE}_\text{sq}(\mathbf{x}^{n}_{\hat{a}}, e), \hat{d}^2)}{\partial d^{\text{PE}}}\right)
    = \frac{1}{2}A_{\hat{a}} \left(-\frac{\partial b( d^\text{PE}_\text{sq}(\mathbf{x}^{n}_{\hat{a}}, e), \hat{d}^2)}{\partial d^{\text{PE}}_\text{sq}} \frac{\partial d^\text{PE}_\text{sq}}{\partial d^\text{PE}}\right) \\
    & = \frac{1}{2} A_{\hat{a}} \left(-\frac{\partial b( d^\text{PE}_\text{sq}(\mathbf{x}^{n}_{\hat{a}}, e), \hat{d}^2)}{\partial d^{\text{PE}}_\text{sq}}\right) 2 d^\text{PE}.
\end{aligned}
$$

According to the scaled barrier function taking squared distance as input (Equation {{eqref: eq:lec21:scaled_barrier_func}}), we can derive

$$
\frac{\partial b(d_{\text{sq}}, \hat{d}^2)}{\partial d_{\text{sq}}} = 
\begin{cases}
    \frac{\kappa}{8} \hat{d} \left( \frac{1}{\hat{d}^2} \ln{\frac{d_{\text{sq}}}{\hat{d}^2}} + \frac{1}{d_{\text{sq}}}\left(\frac{d_{\text{sq}}}{\hat{d}^2} - 1\right) \right) & \text{if } d < \hat{d}; \\
    0 & \text{if } d \geq \hat{d}.
\end{cases}
$$


> **{{rem}}{rm:lec22:set_difference_friction}** The set of point-edge pairs for friction in our semi-implicit friction setting is fixed in each time step and is different from the set of normal contact pairs. The set for friction only contains those pairs with $d^\text{PE}(\mathbf{x}^{n}_{\hat{a}}, e) < \hat{d}$, and this does not change with the optimization variable $\mathbf{x}^{n+1}$ in the current time step.

Now for the tangent information, the key is to keep the normal and the barycentric coordinate of the closest point on the edge constant. For the $k$-th point-edge pair, if we denote the node indices for the point and edge as $p$, $e_0$, and $e_1$, then we can write the relative sliding velocity as

$$
\mathbf{v}_k = (\mathbf{I} - \mathbf{n}\mathbf{n}^T) \big( \mathbf{v}_p - ((1 - r) \mathbf{v}_{e_0} + r \mathbf{v}_{e_1}) \big),
$$

where $r = \argmin_{c} \| \mathbf{x}_p - ((1-c) \mathbf{x}_{e_0} + c \mathbf{x}_{e_1})\|$ is the barycentric coordinate and $\mathbf{n} = \big( \mathbf{x}_p - ((1-r) \mathbf{x}_{e_0} + r \mathbf{x}_{e_1}) \big) / \| \mathbf{x}_p - ((1-r) \mathbf{x}_{e_0} + r \mathbf{x}_{e_1})\|$ is the normal of the edge. Here we see that $r$ and $\mathbf{n}$ are both dependent on $\mathbf{x}$, so directly integrating $\mathbf{v}_k$ is nontrivial. By calculating $\mathbf{n}$ and $r$ using $\mathbf{x}^{n}$, we obtain the semi-implicit relative sliding velocity

$$
\bar{\mathbf{v}}_k = (\mathbf{I} - \mathbf{n}^{n}(\mathbf{n}^{n})^T) \big( \mathbf{v}_p - ((1 - r^{n}) \mathbf{v}_{e_0} + r^{n} \mathbf{v}_{e_1}) \big),
$$

and now only the velocities are dependent on $\mathbf{x}^{n+1}$, which makes both integration and differentiation straightforward. If we denote $\hat{\mathbf{v}}_k =  \mathbf{v}_p - ((1 - r^{n}) \mathbf{v}_{e_0} + r^{n} \mathbf{v}_{e_1})$, we obtain

$$
\frac{\partial \bar{\mathbf{v}}_k}{\partial \hat{\mathbf{v}}_k} = (\mathbf{I} - \mathbf{n}^{n}(\mathbf{n}^{n})^T) \quad \text{and} \quad 
\frac{\partial \hat{\mathbf{v}}_k}{ \partial [\mathbf{x}_p^T, \mathbf{x}_{e_0}^T, \mathbf{x}_{e_1}^T]^T } = \frac{1}{\hat{h}} \begin{bmatrix}\mathbf{I} & (r^{n} - 1) \mathbf{I} & -r^{n} \mathbf{I}\end{bmatrix}.
$$

### Code

Next, let's look at the code. {{ref: imp:lec22:contact_point_and_normal}} calculates the barycentric coordinate of the closest point and the normal given point-edge nodal positions. The idea is to orthogonally project $\mathbf{x}_p$ onto the edge.


{{imp}}{imp:lec22:contact_point_and_normal}[Calculating contact point and normal, PointEdgeDistance.py]
```python
{{#include solid-sim-tutorial/8_self_friction/distance/PointEdgeDistance.py:tangent}}
```

Then, {{ref: imp:lec22:fric_precomp}} traverses all non-incident point-edge pairs with a distance smaller than $\hat{d}$, calculates $\lambda$, and calls the above function to calculate $\mathbf{n}$ and $r$. 

As in [Frictional Contact](./lec9-friction.md), these lines of code are executed at the beginning of each time step in `time_integrator.py`, and the information for each friction pair is stored and passed to the energy, gradient, and Hessian computation functions that we will discuss next.

{{imp}}{imp:lec22:fric_precomp}[Semi-implicit friction precomputation, BarrierEnergy.py]
```python
{{#include solid-sim-tutorial/8_self_friction/BarrierEnergy.py:fric_precomp}}
```
