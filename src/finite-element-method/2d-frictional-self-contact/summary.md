## Summary

We implemented semi-implicit friction in 2D based on squared unsigned distances of point-edge pairs and incorporated it into the time-stepping optimization.

We began by making the friction force integrable in the continuous setting through semi-implicit temporal discretization and a smooth approximation of the dynamic-static friction transition. The spatial discretization of the approximate friction potential follows a similar approach to the barrier potential.

Next, we examined the computation of the normal force magnitude $\lambda$, normal direction $\mathbf{n}$, and barycentric coordinate $r$ of the closest point for point-edge pairs. These values are calculated at the beginning of each time step and remain constant during the optimization. It is important to note that the set of point-edge pairs for friction is also constant per optimization and differs from the set used for the barrier.

Finally, we implemented the computation of the discrete friction potential and its derivatives. We used relative velocities $\hat{\mathbf{v}}_k$ as intermediate variables and applied the chain rule to organize the calculations.

Up to now, we have covered both the theoretical and practical aspects of a 2D solid simulator with inversion-free elastodynamics and interpenetration-free frictional self-contact. Next, we will explore the additional steps needed to extend these concepts to 3D!
