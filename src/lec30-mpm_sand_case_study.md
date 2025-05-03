# Case Study: 2D Sand with a Sphere Collider*

Building on the previous chapter—Two Colliding Elastic Blocks in 2D, where we implemented a minimal Material Point Method (MPM) simulation using the PIC transfer scheme—this case study demonstrates how, with minimal additional effort, we can extend the system to create a more advanced sand simulation.

In this case study, the sand is modeled using the **Drucker-Prager elastoplasticity** @@klar2016drucker constitutive model ([Section 30.1](./lec30.1-drucker_prager.md)), allowing us to capture non-recoverable deformation and internal friction—key features in granular material behavior.

We place a **static sphere collider** inside the domain ([Section 30.2](./lec30.2-sphere_sdf.md])), which interacts with falling sand particles through frictional contact. The collider boundary is represented using a **signed distance function (SDF)** and enforces contact constraints and Coulomb friction.

We extend the original PIC scheme by incorporating the APIC transfer scheme to achieve improved accuracy and reduced numerical dissipation ([Section 30.3](./lec30.3-apic_transfer.md])).

The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `11_mpm_sand` folder.