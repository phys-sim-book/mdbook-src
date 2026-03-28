# Moving Boundary Conditions*

Kinematic Collision Objects (CO) and Moving Dirichlet Boundary Conditions (BC) are crucial in many simulation scenarios. A CO can be considered as a collection of BC nodes.

At the start of a time step, it is ideal if the BC nodes can be moved directly to their prescribed locations without causing any interpenetrations. This allows the simulation to proceed smoothly using the [Degree of Freedom (DOF) elimination method](lec5.2-DOF_elimin.md), which ensures the constraints remain feasible.

However, with large time steps, high velocities, or significant deformations, directly prescribing BC nodes often leads to interpenetration or "tunneling" artifacts, where objects pass through each other unrealistically.

To address these challenges, the penalty method is applied. This method progressively adjusts the simulation towards a feasible set where both CO and BC constraints are satisfied, and interpenetrations are avoided.

A case study demonstrating these principles will be shown through the simulation of a compressed square.
