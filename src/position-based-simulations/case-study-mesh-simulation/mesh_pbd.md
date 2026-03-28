# Case Study: Position-Based Mesh Simulation*

This case study demonstrates how to implement a complete volumetric mesh simulation system.

The core algorithm implements two fundamental constraint types: distance constraints for stretching resistance and volume constraints for incompressibility. The simulation includes advanced features like decoupled simulation and visual meshes with barycentric skinning, enabling high-quality rendering while maintaining efficient simulation.

For implementation, we use [Taichi](https://docs.taichi-lang.org/) as our programming framework, which provides efficient GPU parallelism essential for high-performance volumetric simulations with complex mesh topologies.

The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `13_pbd_mesh` folder.