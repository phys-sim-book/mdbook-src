## Summary

We introduced the Coulomb friction model, which non-smoothly penalizes shearing motion at contact points through static and dynamic friction forces in the tangent space.

To integrate friction into the optimization time integrator, we first smoothly approximate the dynamic-static transition. This allows friction forces to be uniquely determined using only the nodal velocity degrees of freedom.

We then apply a semi-implicit discretization that fixes the normal force magnitude $\lambda$ and the tangent operator $T$ at the previous time step, enhancing the integrability of friction. 

To achieve a solution with fully-implicit friction, fixed-point iterations are performed. These iterations alternate between semi-implicit time integration and updates for $\lambda$ and $T$.

In the next lecture, we will explore a case study involving a square on a slope with varying friction coefficients.
