# Modal Reductions

*Author of this lecture: [Žiga Kovačič](https://zzigak.github.io/), Cornell University*. The techniques presented in this lecture are mostly based on {{#cite sifakis2012finite}}.


Physics-based simulations using the Finite Element Method offer remarkable realism, but this fidelity comes at a high computational cost. A typical high-resolution 3D object can easily have thousands or millions of degrees of freedom (DOFs), leading to large systems of equations that are expensive to solve at each time step. For applications requiring real-time interaction, such as video games, the cost of a full simulation is often prohibitive. This lecture introduces **model reduction**, a powerful family of techniques designed to drastically reduce this computational burden by approximating the system's behavior in a much lower-dimensional space.


The core idea is to move from a high-dimensional state vector $\mathbf{u} \in \mathbb{R}^{3n}$ (where $n$ is the number of vertices) to a much smaller vector of reduced coordinates $\mathbf{q} \in \mathbb{R}^r$ (where $r \ll 3n$). We achieve this by projecting the full system's dynamics onto a carefully chosen low-dimensional subspace that captures the most significant deformation behaviors of the object. 

We will begin by exploring model reduction for linear systems through **linear modal analysis**, which provides an intuitive foundation based on an object's natural vibration modes. We will then extend these concepts to handle large, **nonlinear deformations**, addressing the challenges that arise. A crucial component of any model reduction scheme is the selection of a high-quality subspace basis; we will investigate two prominent approaches: data-driven methods using Principal Component Analysis (PCA) and physics-based methods using **modal derivatives**.