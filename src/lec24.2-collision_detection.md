## Collision Detection

Collision detection in 3D can be significantly more computationally intensive than in 2D due to the larger number of surface primitives involved. Thankfully, spatial data structures like spatial hashing and bounding volume hierarchies (BVH) help efficiently reduce the number of candidate primitive pairs, making continuous collision detection (CCD) more manageable.

### Spatial Hashing

The core idea of spatial hashing is to partition the space into a uniform grid and assign each grid cell an array to store the indices of primitives whose bounding boxes intersect with that cell. To find the nearby primitives of a given primitive $A$ (e.g., a point), we identify the grid cells intersecting with $A$'s bounding box and retrieve the primitive indices stored in these cells. This approach ensures that only nearby primitives are checked for collisions using CCD, eliminating the need for a nested loop to examine all primitive pairs.

### Bounding Volume Hierarchies (BVH)

BVH is another effective method for broad-phase collision detection. It organizes primitives into a hierarchy of bounding volumes, allowing for efficient pruning of the search space when detecting potential collisions.

### ACCD Method

The ACCD (Adaptive Continuous Collision Detection) method, as discussed in [Continuous Collision Detection](./lec21.4-ccd.md), is applicable in 3D. In this context, the distance calculations need to be adapted for point-triangle and edge-edge pairs.

### Broad Phase Collision Detection

For computing the barrier potential energy, gradient, and Hessian, it is faster and essential to first gather a set of nearby candidate primitive pairs. Then, we compute their distances to determine if they are active (i.e., within a distance $\hat{d}$). This filtering process is part of the broad-phase collision detection and can be efficiently implemented using spatial hashing or BVH.

By employing these spatial data structures, we significantly reduce the computational load, focusing our detailed collision checks on a manageable subset of nearby primitives.
