# Case Study: Plastic Material Models*

**Author of this lecture: [Chang Yu](https://changyu.io/), University of California, Los Angeles*

Building on the previous chapter—Two Colliding Elastic Blocks in 2D, where we implemented a minimal Material Point Method (MPM) simulation using the PIC transfer scheme—this case study demonstrates how, with minimal additional effort, we can extend the system to simulate advanced plastic materials.

This case study covers two important plastic material models. First, we demonstrate [Drucker-Prager Elastoplasticity](./lec30.1-drucker_prager.md) through a sand simulation with a sphere collider. Then, we present a [viscoplastic material model](./lec30.4-2d_viscoplastic_bars.md) through a two-bar simulation.

## Sand Simulation with Drucker-Prager Model

For the sand simulation, we use [Poisson-disk Sampling](./lec26-mpm_disc.md) to initialize the material points, which helps reduce aliasing artifacts and structured noise, producing more physically realistic behavior in granular simulations.

The sand is modeled using the [Drucker-Prager Elastoplasticity](./lec30.1-drucker_prager.md) {{#cite klar2016drucker}} constitutive model, allowing us to capture non-recoverable deformation and internal friction—key features in granular material behavior.

We place a [Static Sphere Collider](./lec30.2-sphere_sdf.md) inside the domain, which interacts with falling sand particles through frictional contact. The collider boundary is represented using a **signed distance function (SDF)** and enforces contact constraints and Coulomb friction.

We extend the original PIC scheme by incorporating the [APIC Transfer Scheme](./lec30.3-apic_transfer.md) to achieve improved accuracy and reduced numerical dissipation.

## Viscoplastic Material Model

**Author of this section: [Žiga Kovačič](https://zzigak.github.io), Cornell University*

Additionally, we demonstrate a **viscoplastic** material model in [2D Viscoplastic Bars](./lec30.4-2d_viscoplastic_bars.md), which extends traditional plasticity with rate-dependent behavior. This model captures materials like toothpaste or highly viscous fluids, where resistance to deformation depends on the rate of loading.

The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `12_mpm_plastic` folder.