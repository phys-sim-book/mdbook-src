# Beyond Elasticity: Plasticity and Viscosity

**Author of this lecture: [Chang Yu](https://changyu.io/), University of California, Los Angeles*

In physics-based simulation, the **continuum assumption** enables us to model a wide range of materials with rich and diverse behaviors. While **hyperelasticity** provides a convenient and elegant model for materials that return to their rest shape after deformation, such as rubber or soft tissue, it represents only a small portion of the physical phenomena observed in the real world.

Many natural and everyday materials, including **sand**, **snow**, **mud**, **metal**, **foam**, and **fracturing solids**, exhibit irreversible deformation, dissipation, or flow-like behavior that cannot be captured by purely elastic models. These materials require constitutive models that incorporate **plasticity** (permanent deformation) and **viscosity** (rate-dependent stress response).

The core difference between **elasticity** and **plasticity** lies in how materials store and dissipate energy. Elastic deformation is reversible and energy-conservative; plastic deformation is **irreversible**, governed by **yield criteria**, and involves **energy dissipation** through internal reconfiguration of the material structure.

MPM is particularly well suited for simulating plastic and viscous materials, as it naturally supports large deformations, history-dependent state updates, and localized inelastic flow, without suffering from mesh entanglement, element inversion, or remeshing-related artifacts common in traditional mesh-based methods.

In the remainder of this lecture, we will introduce how plasticity and viscosity are incorporated into MPM simulations. We begin with the [discretization of plastic flow](./lec27.1-disc_plastic_flow.md) and the representation of irreversible deformation. We then describe how to enforce material-specific yield conditions and perform [return mapping](./lec27.2-yield_criterion.md) to constrain deformation within admissible limits.