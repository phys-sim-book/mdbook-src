## Summary

We have extended our [Elastic MPM Framework](./lec29-mpm_elastic_case_study.md) to simulate plastic materials by incorporating two key material models: **Drucker-Prager elastoplasticity** for granular materials and **viscoplasticity** for rate-dependent materials.

For the sand simulation, we use **Poisson-disk sampling**, **APIC transfers**, and **SDF-based frictional contact**. The APIC scheme improves accuracy while maintaining stability by capturing local affine motion, and the Drucker-Prager model enables realistic plastic flow and pressure-dependent yielding. A static sphere collider is introduced using a signed distance function (SDF), allowing smooth and robust enforcement of contact and friction constraints.

The viscoplastic model extends traditional plasticity with rate-dependent behavior, where the yield stress depends on the plastic strain rate. This enables realistic simulation of materials like toothpaste or highly viscous fluids, where resistance to deformation increases with loading rate.

Together, these enhancements enable stable and physically plausible simulation of various plastic materials undergoing large deformation.