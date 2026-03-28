# Case Study: Position-Based Cloth Simulation*


Building on the theoretical foundation established in [Position-Based Dynamics: General Framework](../lec31-position_based_dynamics.md), this case study demonstrates how to implement a complete cloth simulation system. Compared to other PBD domains (fluids and volumetric meshes), cloth presents unique challenges in modeling both stretching resistance and bending behavior through distance and dihedral angle constraints.

The core algorithm implements two fundamental constraint types: distance constraints for stretching resistance and dihedral angle constraints for bending behavior. The simulation includes dynamic constraint solving with compliance parameters, allowing fine control over material properties.

For implementation, we use [Taichi](https://docs.taichi-lang.org/) as our programming framework, which provides efficient GPU parallelism essential for high-performance cloth simulations with complex mesh topologies.

The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `12_pbd_cloth` folder.