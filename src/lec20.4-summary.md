## Summary

We have connected the discrete weak form (Equation {{eqref:eq:lec19:discrete_weak_form_BE}}) to the implementations in [Filter Line Search](./lec8-filter_line_search.md) for boundary conditions and contact. Additionally, we have derived self-contact between discrete surfaces in 2D, which will be implemented in the next lecture.

The derivations follow a consistent methodology: first, rewrite the global integral as a summation of local element-wise integrals, and then approximate or analytically evaluate the local integrals using certain quadrature rules.

We didn't explicitly discuss friction in this lecture because its force definition in the continuous setting was covered in [Boundary Conditions and Frictional Contact](./lec18-BC_and_fric.md). Its integral approximation can be performed similarly to normal contact forces (see [Case Study: 2D Frictional Self-Contact](./lec22-2d_self_fric.md) for details).

During the derivation, we also observed that the route we have taken from the strong form to the optimization time integration implementation, namely:

$$
\begin{aligned}
    & \text{strong form} \rightarrow \text{weak form} \rightarrow \text{discrete weak form} \rightarrow \\
    & \text{finite element approximation} \rightarrow \text{optimization time integration}
\end{aligned}
$$

is not unique. We can directly write the continuous form of the potential energies and then perform spatial discretization and approximation to obtain the nodal forces. Readers interested in this approach can refer to Lagrangian Mechanics or Hamiltonian Mechanics.
