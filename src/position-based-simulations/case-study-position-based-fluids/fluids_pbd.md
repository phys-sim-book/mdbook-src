# Case Study: Position-Based Fluids Simulation*

The Position-Based Dynamics framework can be elegantly extended from solids to simulate incompressible fluids. The resulting method, known as Position-Based Fluids (PBF) {{#cite macklin2013position}}, replaces the complex pressure solves of traditional Smoothed Particle Hydrodynamics (SPH) {{#cite koschier2020smoothed}} with a set of geometric constraints. This approach inherits the stability and efficiency of PBD, allowing for large time steps suitable for real-time applications, while effectively enforcing the constant-density condition that characterizes incompressible flow.

Building on the theoretical foundation established in [Position-Based Fluids: Density and Surface Constraints](./lec34.1-position_based_fluids.md), this case study demonstrates how to implement a complete PBF simulation system. Compared to other PBD domains considered before (cloth and volumetric meshes), fluids present unique challenges in enforcing incompressibility through density constraints.

The core algorithm implements the three-phase density constraint solver: computing Lagrange multipliers $\lambda_i$, calculating position corrections using the symmetric constraint formula, and applying corrections with tensile instability prevention.

The simulation includes XSPH viscosity for coherent liquid-like motion and vorticity confinement to reintroduce rotational energy lost during iterative constraint solving. Dynamic walls provide compression scenarios that showcase the method's ability to handle complex boundary interactions.

For implementation, we use [Taichi](https://docs.taichi-lang.org/) as our programming framework, which provides efficient GPU parallelism essential for high-performance particle simulations with spatial hashing.

The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `14_pbf` folder.
