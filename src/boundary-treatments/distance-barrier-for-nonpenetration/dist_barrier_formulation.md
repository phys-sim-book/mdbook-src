## Distance Barrier

### Constrained Optimization

In scenarios like a solid interacting with a planar ground, where the signed distance function \\( d(\mathbf{x}) \\) is smooth outside the obstacle, we can approach the modeling of contact by incorporating non-interpenetration constraints. These constraints are formulated using \\( d(\mathbf{x}) \\), while we also aim to minimize the Incremental Potential of the system.

Assuming that the solids are densely sampled with nodes \\(\mathbf{x}\\), we apply these constraints at the level of nodal Degrees of Freedom (DOFs) in relation to the obstacles:

$$
    \min_{\mathbf{x}} E(\mathbf{x}) \quad \text{s.t.} \quad d_{ij} \geq 0 \ \ \forall \ \text{node} \ i \ \text{and} \ \text{obstacle} \ j.
    {{numeq}}{eq:lec7:constrained_IP}
$$

In this equation, \\( d_{ij} \\) represents the signed distance between node \\( i \\) and obstacle \\( j \\). By ensuring that \\( d_{ij} \\) is non-negative, we effectively prevent the solids from intersecting with the obstacles[^1].

### Logarithm Barrier Potential in Contact Modeling

To address the inequality constraints in our contact modeling, we introduce a barrier potential \\( P_b(\mathbf{x}) \\). This potential transforms the constrained problem, as described in Equation {{eqref: eq:lec7:constrained_IP}}, into an "unconstrained" optimization problem:

$$
    \min_{\mathbf{x}} E(\mathbf{x}) + h^2 P_b(\mathbf{x}).
    {{numeq}}{eq:lec7:barrier_IPC}
$$

The barrier potential is defined as follows:

$$
    P_b(\mathbf{x}) = \sum_{i,j} A_i \hat{d} b(d_{ij}(\mathbf{x})) \quad \text{and} \quad
    b(d_{ij}(\mathbf{x})) = 
    \begin{cases}
        \frac{\kappa}{2} \left(\frac{d_{ij}}{\hat{d}} - 1\right) \ln \frac{d_{ij}}{\hat{d}} & d_{ij} < \hat{d} \\
        0 & d_{ij} \geq \hat{d}.
    \end{cases}
    {{numeq}}{eq:lec7:barrier_func}
$$

In this formulation, \\( b() \\) represents the barrier energy density function. As the distance approaches zero, this function tends to infinity, thereby providing a strong repulsion force to prevent interpenetration (refer to {{ref: fig:lec7:barrier_func}}). The distance threshold \\( \hat{d} \\) above which no contact force is exerted, the contact stiffness \\( \kappa \\) which controls the rate of change of the contact forces with respect to distance, and \\( A_i \\), the contact area of node \\( i \\), are key parameters in this setup. By integrating the energy density over the solid boundary, the barrier formulation effectively models a potential energy field that is of thickness  \\( \hat{d} \\).

<figure>
    <center>
    <img src="img/lec7/barrier_clamping.jpg" width="350">
    </center>
    <figcaption><b>{{fig}}{fig:lec7:barrier_func}</b> The barrier energy density function plotted with different $\hat{d}$. Decreasing $\hat{d}$ asymptotically matches the discontinuous definition of the contact condition. </figcaption>
</figure>

> **{{rem}}{rm:lec7:contact_layer}[Contact Layer Interpretation]**
> Imagine the barrier potential \\( P_b(\mathbf{x}) \\) as representing the elasticity of an ultra-thin layer of virtual material that exists just outside the boundaries of the solids. This virtual layer has an effective thickness of \\( \hat{d} \\), which correlates with the distance threshold in the barrier function.
>
> Consequently, the integration or summation used in computing \\( P_b(\mathbf{x}) \\) is weighted by the volume element \\( w_i = A_i \hat{d} \\), where \\( A_i \\) represents the contact area of each node. As solids approach and begin to compress this virtual elastic layer, contact forces arise. These forces, akin to a unique type of elasticity force, serve to prevent interpenetration by providing a repulsion effect whenever the solids come too close to each other. This model allows us to simulate the physical response of contact without actual penetration of the solids.

Applying chain rules with distance being the intermediate variables, we can derive the gradient and Hessian of \\(P_b(\mathbf{x})\\) as
$$
    \nabla P_b(\mathbf{x}) = \sum_{i,j} w_i \frac{\partial b}{\partial d}(d_{ij}(\mathbf{x})) \nabla d_{ij}(\mathbf{x})
    {{numeq}}{eq:lec7:barrier_grad}
$$
and
$$
    \nabla^2 P_b(\mathbf{x}) = \sum_{i,j} w_i \left( \frac{\partial^2 b}{\partial d^2}(d_{ij}(\mathbf{x})) \nabla d_{ij}(\mathbf{x}) \nabla d_{ij}(\mathbf{x})^T + \frac{\partial b}{\partial d}(d_{ij}(\mathbf{x})) \nabla^2 d_{ij}(\mathbf{x}) \right).
    {{numeq}}{eq:lec7:barrier_hess}
$$

[^1]: As we are using signed distances here, the inequality constraints can be defined without introducing an \\(\epsilon\\) as in Equation {{eqref: eq:lec2:IC}} with unsigned distances.
