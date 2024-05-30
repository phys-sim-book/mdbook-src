## Summary

Simulating solids involves predicting changes in their position and form over time. To achieve this on computers, both geometry and time must be represented discretely.

Geometries are typically represented using sample points interconnected in specific ways:
* **Finite Element Methods (FEM)** connect sample points through unstructured meshes.
* **Material Point Methods (MPM)** utilize uniform Cartesian grids to link sample points.
FEM excels in delivering high-precision results, while MPM is advantageous for handling topological changes. This book primarily focuses on FEM.

Time is discretized into distinct moments, with finite difference methods applied to calculate temporal derivatives of physical quantities, in line with Newton's second law.

The Forward Euler method is generally avoided due to its unconditional instability. Conversely, the Symplectic Euler method is explicit and conditionally stable, often preferred for well-conditioned problems. For stiff problems, the Backward Euler method, unconditionally stable but requiring the resolution of nonlinear equation systems, is commonly used despite its computational intensity and potential for numerical instability.

In the next lecture, we will explore the optimization perspective of implicit time integration, offering robustness in solving these problems.
