## Conservation of Mass

We can think of the mass density $R(\mathbf{X}, t)$ to be naturally defined over $\Omega^0$ as
$$
R(\mathbf{X}, t) = \lim_{\epsilon \to +0} \frac{\text{mass}(B^t_\epsilon)}{\text{volume}(B^t_\epsilon)} = \lim_{\epsilon \to +0} \frac{\text{mass}(B^t_\epsilon)}{\int_{B^t_\epsilon} d\mathbf{x}}
{{numeq}}{eq:lec16:mass_def}
$$
where $B^t_\epsilon$ is the world space counterpart of $B^0_\epsilon$ (the ball of radius $\epsilon$ surrounding an arbitrary $\mathbf{X} \in \Omega^0$).
This is arguably a natural definition since $\text{mass}(B^t_\epsilon)$ should be a measurable quantity. Conservation of mass can be expressed as
$$
\text{mass}(B^t_\epsilon) = \text{mass}(B^0_\epsilon), \quad \forall \ B^0_\epsilon \subset \Omega^0 \ \text{and} \ t \geq 0.
{{numeq}}{eq:lec16:mass_conservation}
$$

<p style="color:red">TODO: modify derivation</p>

Now, with a change of variables, we have $\int_{B^t_\epsilon} d\mathbf{x} = \int_{B^0_\epsilon} J(\mathbf{X}, t) d\mathbf{X}$, so Equation {{eqref: eq:lec16:mass_def}} becomes
$$
R(\mathbf{X}, t) = \lim_{\epsilon \to +0} \frac{\text{mass}(B^t_\epsilon)}{\int_{B^0_\epsilon} J(\mathbf{X}, t) d\mathbf{X}},
{{numeq}}{eq:lec16:R_X_t_formula}
$$
and so
$$
R(\mathbf{X}, 0) = \lim_{\epsilon \to +0} \frac{\text{mass}(B^0_\epsilon)}{\int_{B^0_\epsilon} d\mathbf{X}}
{{numeq}}{eq:lec16:R_X_0_formula}
$$
since $J(\mathbf{X}, 0) = 1$.
Then combining Equations {{eqref:eq:lec16:mass_conservation}}, {{eqref:eq:lec16:R_X_t_formula}}, and {{eqref:eq:lec16:R_X_0_formula}}, we can express the conservation of mass as
$$
\int_{B^0_\epsilon} R(\mathbf{X}, t) J(\mathbf{X}, t) d\mathbf{X} = \int_{B^0_\epsilon} R(\mathbf{X}, 0) d\mathbf{X}, \quad \forall \ B^0_\epsilon \subset \Omega^0 \ \text{and} \ t \geq 0.
$$
This just says that the mass in $B^t_\epsilon$ (as expressed via an integral of the mass density) should not change with time. This set is associated with a subset of the material at time $t$ and as it evolves in the flow, the material will take up more or less space, but there will always be the same amount (mass) of material in the set. Since $B^0_\epsilon$ is arbitrary, it must be true that
$$
R(\mathbf{X}, t) J(\mathbf{X}, t) = R(\mathbf{X}, 0), \quad \forall \ \mathbf{X} \in \Omega^0 \ \text{and} \ t \geq 0.
$$

> **{{rem}}{rem:lec16:lag_and_aug}[Lagrangian and Eulerian Views]** 
In simulation methods that discretize and track materials directly based on $\Omega^0$, conservation of mass is inherently satisfied. For instance, in our Finite Element Method (FEM) simulator, $\Omega^0$ is segmented into triangles, with the mass of each triangle remaining constant regardless of deformation throughout the simulation. This approach is known as the **Lagrangian method**. In contrast, **Eulerian methods** discretize and evolve physical quantities based on $\Omega^t$ and often need to specially deal with mass conservation.

