## Choice of Basis for Nonlinear Deformations

The quality of the reduced model is determined by the choice of the time-invariant basis matrix $\mathbf{U} \in \mathbb{R}^{3n \times r}$, which we assumed existance of in the previous chapters. This basis, which is pre-processed to be mass-orthonormal ($\mathbf{U}^T\mathbf{M}\mathbf{U} = \mathbf{I}$), must effectively span the space of expected nonlinear deformations.

#### Basis from Simulation Data (POD)
This data-driven approach, also known as **Principal Orthogonal Directions (POD)**, constructs an optimal basis from pre-existing simulation data.

First, an offline, unreduced simulation is run to generate a set of $N$ deformation state vectors $\{\mathbf{u}_1, \mathbf{u}_2, ..., \mathbf{u}_N\}$.
Afterwards, the snapshots are assembled into a matrix $\mathbf{A} = [\mathbf{u}_1, \mathbf{u}_2, ..., \mathbf{u}_N]$. A basis is extracted by performing a Singular Value Decomposition (SVD), typically with respect to the mass-weighted inner product $\langle\mathbf{x}, \mathbf{y}\rangle_\mathbf{M} = \mathbf{x}^T\mathbf{M}\mathbf{y}$ (a technique known as mass-PCA). The columns of $\mathbf{U}$ from the decomposition $\mathbf{A} = \mathbf{U}\Sigma\mathbf{V}^T$ that correspond to the largest $r$ singular values form the basis $\mathbf{U}$!

While optimal for the training data, this method's primary drawback is the necessity of a slow, offline pre-simulation.

#### Basis from Modal Derivatives
This approach constructs a basis automatically by analyzing the system's fundamental nonlinear response, without requiring a non-reduced pre-simulation like POD.

The key insight is that linear modes are an incomplete basis. When a nonlinear system is excited in the direction of a linear mode, other deformations **naturally co-appear** due to nonlinear coupling. Modal derivatives are precisely these coupled deformations.

Mathematically, we analyze the static deformation $\mathbf{u}(\mathbf{p})$ resulting from a force applied along the linear modes $\mathbf{U}_{lin}$: $\mathbf{f}_{int}(\mathbf{u}) = \mathbf{M}\mathbf{U}_{lin}\mathbf{\Lambda}\mathbf{p}$, where $\mathbf{\Lambda}$ is the diagonal matrix of squared frequencies and $\mathbf{p}$ is a parameter vector. The solution $\mathbf{u}(\mathbf{p})$ can be expressed as second order Taylor series expansion around $\mathbf{p}=\mathbf{0}$:

$$ \mathbf{u}(\mathbf{p}) = \sum_{i=1}^k \psi_i p_i + \frac{1}{2} \sum_{i=1}^k \sum_{j=1}^k \mathbf{\Phi}_{ij} p_i p_j + O(||\mathbf{p}||^3)$$


This expansion reveals the system's response structure. The first-order derivatives, $\psi_i = \frac{\partial \mathbf{u}}{\partial p_i}|_{\mathbf{p}=\mathbf{0}}$, are precisely the familiar linear vibration modes, representing the initial linear response to the applied force. The crucial nonlinear couplings are captured by the second-order derivatives, $$\mathbf{\Phi}_{ij} = \frac{\partial^2 \mathbf{u}}{\partial p_i \partial p_j}|_{\mathbf{p}=\mathbf{0}},$$ which are known as the **modal derivatives**. Each $\mathbf{\Phi}_{ij}$ is a deformation vector that can be computed by solving a linear system involving the constant, rest-state stiffness matrix $\mathbf{K}$.


The full set of vectors $\{\psi_i, \mathbf{\Phi}_{ij}\}$ is typically too large. A compact, $r$-dimensional basis $\mathbf{U}$ is formed by collecting all linear modes and modal derivatives, scaling them to prioritize low-frequency contributions, and then using mass-PCA to extract the most significant combined shapes. This produces a general-purpose basis that captures essential nonlinear behavior without any prior knowledge of runtime forces.

For more information and the derivation please referr to SIGGRAPH notes from {{#cite sifakis2012finite}}.