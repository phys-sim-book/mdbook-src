# lec25.3 - Summary

This lecture explores how subspace methods can be leveraged to extend the IPC framework to rigid and near-rigid body simulations effectively. The key idea is to reduce the system's degrees of freedom (DOFs) by projecting the high-dimensional deformable body dynamics into a lower-dimensional subspace that still captures essential behavior. 

Rigid body dynamics are introduced from Newtonian mechanics, resulting in the standard equations governing linear and angular momentum evolution. Integration schemes for solving these equations are reviewed, with quaternions used for rotation tracking.

Affine Body Dynamics (ABD) {{#cite lan2022affine}} is proposed as a compromise between rigid and deformable models, allowing for minimal affine deformation while maintaining computational efficiency. The ABD formulation benefits from linear dependence of vertex positions on the Projected Newton optimization step size, enabling effective use of linear CCD for collision resolution in IPC.

The ABD model allows simulation using significantly fewer DOFs by driving high-resolution collision geometry via low-resolution affine embeddings (e.g., triangle-driven motion). IPC gradients and Hessians are projected through the Jacobian to solve the reduced system in the subspace, leading to faster and more stable simulations for complex scenes with many rigid-like bodies and dense contacts.

In summary, this chapter presents a practical pathway to integrate IPC with rigid and near-rigid body simulations using subspace embeddings, especially affine-based, while retaining robustness and scalability in contact-rich scenarios.
