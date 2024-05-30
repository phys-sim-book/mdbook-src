## Summary

In this case study, we implemented semi-implicit friction between simulated objects and a slope, accommodating arbitrary orientations and positions. Within the optimization time integration framework of IPC, friction is also modeled using potential energy. The key difference is that the normal force magnitude and tangent operator are precomputed at the start of each time step for semi-implicit discretization.

In the next lecture, we will introduce moving boundary conditions. This will involve obstacles or boundary nodes moving in a prescribed manner, actively injecting dynamics into the scene.
