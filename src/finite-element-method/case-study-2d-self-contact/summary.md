## Summary

We have implemented frictionless self-contact with guaranteed non-intersection for 2D FEM simulations by discretizing barrier energies onto the non-incident point-edge pairs on the boundary.

To compute the barrier energies, we used squared point-edge distances to avoid potential numerical issues. The point-edge distance is a piecewise smooth function with closed-form expressions depending on the relative positions of the point and the edge, and the overall function is $C^1$-continuous everywhere. The derivatives of the function can be conveniently obtained by applying symbolic differentiation to each expression.

For line search filtering, instead of directly computing the time of impact (TOI) which is prone to numerical issues, we implemented the additive CCD method (ACCD) to obtain a sufficiently large and conservative estimate of TOI. ACCD is an iterative method that accumulates lower bounds of TOI while progressively advancing the nodes along the search direction. Before running ACCD, we perform overlap checks on the bounding boxes of the point's and edge's spans to quickly filter out non-colliding pairs.

In later lectures, we will see that for large-scale scenes in 3D, efficient spatial indexing strategies such as spatial hashing and bounding box hierarchies (BVH) will be needed to significantly reduce the expensive spatial search costs.

In the next lecture, we will implement frictional self-contact based on what we have just developed.
