# Case Study: 2D Sand with a Sphere Collider*

So far, we have introduced all core components necessary to build a complete and physically plausible MPM simulation system—including material discretization, time integration, plasticity modeling, and boundary handling.

In this case study, we put these components into action to simulate a **2D sand system** under gravity. The sand is modeled using the **Drucker-Prager elastoplasticity** @@klar2016drucker constitutive model ([Section 30.1](./lec30.1-drucker_prager.md)), allowing us to capture non-recoverable deformation and internal friction—key features in granular material behavior.

We place a **static sphere collider** inside the domain ([Section 30.2](./lec30.2-sphere_sdf.md])), which interacts with falling sand particles through frictional contact. The collider boundary is represented using a **signed distance function (SDF)** and enforces contact constraints and Coulomb friction.

For implementation ([Section 30.3](./lec30.3-mpm_impl.md)), we use NumPy and [Taichi](https://docs.taichi-lang.org/) as our programming framework. Taichi provides efficient parallelism on both CPU and GPU, and more importantly, it supports [sparse data structures]((https://docs.taichi-lang.org/docs/sparse)), which are critical for high-performance MPM grid computations.

The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `11_mpm_sand` folder.