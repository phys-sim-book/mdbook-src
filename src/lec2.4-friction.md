## Friction

Friction is a crucial element in physical interactions involving movement, often significantly influencing simulation outcomes. Thus, its precise modeling is vital for realistic and reliable simulations. See {{ref: fig:lec2:contact_and_friction}} on the right for a demonstration of a scenario that requires a precise representation of friction.

One of the most widely adopted models for friction is the **Coulomb Friction model**. This model hinges on the **Maximal Dissipation Principal (MDP)**, effectively capturing the nonsmooth transition between static and dynamic frictions. Static friction is the force preventing an object from initiating movement, whereas dynamic friction, or kinetic friction, opposes the motion of a moving object. The Coulomb Friction model accurately depicts the critical transition between these two friction types.

In the standard Material Point Method (MPM), friction is inherently modeled by the grid. However, this method has its drawbacks, notably an uncontrollable and unrealistically large friction coefficient.

For the Finite Element Method (FEM), friction can be more realistically and controllably represented through an approximated potential energy in the Incremental Potential Contact (IPC) model. This fits well within our optimization time integration framework. By using potential energy to approximate friction, we not only maintain the robustness of the simulation but also gain control over the accuracy of the friction model.

In subsequent lectures, we will delve into the specific techniques and methodologies employed in the IPC model to represent friction forces and their role in enhancing the accuracy and realism of simulations.