## Summary

Based on the temporally and spatially discretized weak form, we've explored methods to compute the mass matrix, deformation gradient, and elasticity force under the linear finite element setting, all of which align with our implementation in Section [Inversion-Free Elasticity](./lec15-inv_free_elasticity.md).

With linear finite elements, the world space coordinates $\mathbf{x}$ are approximated as a piecewise linear function of $\mathbf{X}$. This approximation, $\hat{\mathbf{x}}(\mathbf{X})$, is a linear function inside each triangle and is $C^0$-continuous at the edges. By using two parameters, $\beta$ and $\gamma$, to represent points on each triangle, we can identify the linear shape functions that interpolate the displacements at the triangle vertices and derive the deformation gradient $\mathbf{F}$. The mass matrix entries and elasticity terms can then be computed via integration with respect to $\beta$ and $\gamma$.
