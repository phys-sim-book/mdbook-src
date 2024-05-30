## Summary

In this lecture, we derived the strong forms of the governing equations—conservation of mass and conservation of momentum—focusing on an infinitesimal region within the simulation domain. The conservation of momentum equation was transformed from surface to volume integrals using the divergence theorem.

For Lagrangian simulation methods, such as FEM solid simulation, which discretize and monitor physical quantities based on the material space $\Omega^0$, the conservation of mass is inherently maintained. We then progressed to deriving the weak form of conservation of momentum. This involved integrating the dot product between the momentum terms and an arbitrary test function. The weak form is effectively equivalent to the strong form because the integral equation must satisfy any arbitrary test function. Techniques such as integration by parts and the application of the divergence theorem were essential in this derivation.

In our next lecture, we will discretize the weak form both temporally and spatially, further refining our approach to solve the discrete problems examined in our case studies.
