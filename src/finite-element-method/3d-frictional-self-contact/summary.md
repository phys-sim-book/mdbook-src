## Summary

In this section, we discussed the main technical details of implementing a 3D contact handling method based on Implicit Contact Prediction (IPC).

In 3D, both distance and friction basis computations become more complex. These computations rely on point-triangle and edge-edge primitive pairs, similar to the point-edge pairs used in 2D.

For edge-edge distances, which are only $C^0$-continuous, an additional mollification function that smoothly decreases to zero is necessary. This function is multiplied with the barrier energy density function to achieve $C^1$-continuity, enabling the use of efficient gradient-based optimization methods.

Due to the significantly larger number of primitive pairs in 3D, spatial data structures like spatial hashing or bounding volume hierarchies (BVH) are often used in the broad phase to filter candidates before computing distances or performing CCD.
