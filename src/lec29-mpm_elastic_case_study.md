# Case Study: 2D Sand with a Sphere Collider*

So far, we have introduced all core components necessary to build a complete and physically plausible MPM simulation system—including material discretization, time integration, and data transfer schemes.

In this case study, we put these components into action to simulate a 2D collision of two elastic blocks. We start by [setting up the simulation](./lec29.1-simulation_setup.md) with material properties and data structure definitions. Then, we implement the [Particle-In-Cell (PIC) Transfer](./lec29.2-pic_transfer.md) to handle momentum exchange between particles and the grid.

This example serves as a **quick-start** demonstration of the MPM pipeline using elastic materials, without plasticity or complex boundary conditions.

For implementation, we use NumPy and [Taichi](https://docs.taichi-lang.org/) as our programming framework. Taichi provides efficient parallelism on both CPU and GPU, and more importantly, it supports [sparse data structures]((https://docs.taichi-lang.org/docs/sparse)), which are critical for high-performance MPM grid computations.

The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `10_mpm_elasticity` folder.