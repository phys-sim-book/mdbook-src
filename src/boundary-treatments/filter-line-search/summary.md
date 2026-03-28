## Summary

To mitigate tunneling issues in solid simulation with large time steps, it is crucial to enforce non-negativity constraints of signed distances between solids and obstacles throughout the entire motion trajectory, not just at the final solution.

While directly using the optimization path to approximate the motion trajectory isn't perfect theoretically, it supports the design of a filter line search scheme. This scheme utilizes continuous collision detection (CCD) and the projected Newton method, effectively preventing tunneling in practical scenarios.

The projected Newton method, a gradient-based approach for minimizing the Incremental Potential, requires that the potential energy has a continuous gradient. Consequently, the distance functions employed in our barrier potential need to be at least $C^1$ continuous. For grid-based signed distance fields ({{ref: exp:lec7:grid_sdf}}), mere bilinear interpolation is considered insufficient.

Additionally, handling self-contact on the piece-wise linear boundary of a mesh necessitates further approximations to smooth the distance function. Detailed exploration of self-contact will be addressed in [future sections](lec20-pw_linear_boundary.md). Before that, we will first transition to discussing solids-obstacle friction in our next lecture.
