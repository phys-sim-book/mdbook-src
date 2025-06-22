## Linear Modal Analysis

We begin our exploration of model reduction with the simplest case: linear elasticity. While limited to small deformations, this context provides a clear and intuitive introduction to the core concepts of subspace simulation.

### The Physics of Vibration Modes

Imagine striking a tuning fork or plucking a guitar string. The object doesn't deform in a random way; instead, it vibrates in a superposition of specific, characteristic patterns called **modes**. Each mode has an associated natural frequency. Low-frequency modes correspond to large, low-energy deformations (like the fundamental bending of the tuning fork), while high-frequency modes represent complex, high-energy vibrations that are often difficult to see and are quick to dissipate.

The key insight is that the vast majority of an object's visible dynamic behavior can be described by a small number of these low-frequency modes. By restricting our simulation to only these important modes, we can create a highly efficient approximation. It is crucial to remember that this approach is valid only for small deformations, where the material's response remains approximately linear. For large deformations, the stiffness properties change with the deformation, and linear modal analysis is no longer applicable.


### Computing the Modes

Finding these modes requires us to solve a generalized eigenvalue problem. The setup involves first defining the full system and then applying constraints.
1. We start by forming the full mass matrix $\mathbf{M} \in \mathbb{R}^{3n \times 3n}$ and stiffness matrix $\mathbf{K} \in \mathbb{R}^{3n \times 3n}$.
2. To account for Dirichlet boundary conditions (fixed vertices), we form smaller, constrained matrices $\hat{\mathbf{M}}$ and $\hat{\mathbf{K}}$ by removing the rows and columns from $\mathbf{M}$ and $\mathbf{K}$ that correspond to the fixed degrees of freedom.

The eigenvalue problem is then solved on these constrained matrices:
$$
\hat{\mathbf{K}}\hat{\mathbf{\psi}}_i = \lambda_i \hat{\mathbf{M}}\hat{\mathbf{\psi}}_i
{{numeq}}{eq:lec26:linear_system}
$$

{{exp}}{exp:lec25:eigenproblem_intuition}[Interpreting the Generalized Eigenvalue Problem]
The equation {{eqref:eq:lec26:linear_system}} is fundamental to understanding vibrations in mechanical systems. Let's break it down:
*   $\mathbf{K} \in \mathbb{R}^{3n \times 3n}$ is the full **stiffness matrix**. It relates displacement to internal elastic forces ($\mathbf{f}_{elastic} = -\mathbf{K}\mathbf{u}$). A high value in $\mathbf{K}$ means the object strongly resists deformation.
*   $\mathbf{M} \in \mathbb{R}^{3n \times 3n}$ is the full **mass matrix**. It relates acceleration to inertial forces ($\mathbf{f}_{inertial} = \mathbf{M}\mathbf{a}$).
*   An **eigenvector** $\mathbf{\psi}_i \in \mathbb{R}^{3n}$ is a **mode shape**. It's a vector that describes the spatial pattern of a particular vibration mode.
*   An **eigenvalue** $\lambda_i \in \mathbb{R}$ is the squared **natural frequency**, for the corresponding mode $\mathbf{\psi}_i$.

The equation $\mathbf{K}\mathbf{\psi}_i = \lambda_i \mathbf{M}\mathbf{\psi}_i$ essentially states that a modal deformation $\mathbf{\psi}_i$ is special: if the object is deformed into this shape, the elastic restoring force ($\mathbf{K}\mathbf{\psi}_i$) is perfectly proportional to the inertial force ($\mathbf{M}\mathbf{\psi}_i$) required to produce a sinusoidal vibration with that shape.

The **modes** are the shapes where the elastic and inertial forces align perfectly. Low eigenvalues correspond to low-energy modes, which are the most important for visual animation.

The solution from the eigensolver gives us eigenvalues $\lambda_i$ and eigenvectors $\hat{\mathbf{\psi}}_i$ for the unconstrained DOFs. The eigenvalues are the squares of the natural frequencies of vibration, $\lambda_i = \omega_i^2$, and the eigenvectors represent the mode shapes. In order to check that the eigensolver was successful, it is common to visualize the individual modes by animating them as $\mathbf{\psi}_i\sin(\omega_i t)$, where $t$ is time.

The different modal vectors are typically assembled into a **modal basis matrix**:

$$
\mathbf{U} = [\mathbf{\psi}_1, \dots, \mathbf{\psi}_r] \in \mathbb{R}^{3n \times r},
$$ 

where we select the $r$ modes corresponding to the smallest non-zero eigenvalues. 



### Subspace Simulation of Linear Dynamics

Now, let's see how this modal basis accelerates simulation. The full-space equation of motion for a linear elastic system with damping is:

$$
\mathbf{M}\ddot{\mathbf{u}} + \mathbf{D}\dot{\mathbf{u}} + \mathbf{K}\mathbf{u} = \mathbf{f}_{ext}
{{numeq}}{eq:lec26:main_ode}
$$

where $\mathbf{D}$ is the damping matrix and $\mathbf{f}_{ext}$ is the vector of external forces. To reduce this system, we introduce the central approximation of subspace methods: the full-space displacement $\mathbf{u}$ is approximated by a linear combination of our basis vectors:

$$
\mathbf{u}(t) \approx \mathbf{U}\mathbf{q}(t)
$$

Here, $\mathbf{q}(t) \in \mathbb{R}^r$ is the vector of **reduced coordinates** or **modal amplitudes**, where ideally $r << 3n$. Each component $q_i(t)$ represents "how much" of mode $\mathbf{\psi}_i$ is present in the deformation at time $t$.

To derive the equation of motion for $\mathbf{q}$, we substitute approximation for $u(t)$ into {{eqref:eq:lec26:main_ode}} and project the entire equation onto the subspace by pre-multiplying with $\mathbf{U}^T$:
$$
\mathbf{U}^T (\mathbf{M}(\mathbf{U}\ddot{\mathbf{q}}) + \mathbf{D}(\mathbf{U}\dot{\mathbf{q}}) + \mathbf{K}(\mathbf{U}\mathbf{q})) = \mathbf{U}^T\mathbf{f}_{ext}
$$
$$
(\mathbf{U}^T\mathbf{M}\mathbf{U})\ddot{\mathbf{q}} + (\mathbf{U}^T\mathbf{D}\mathbf{U})\dot{\mathbf{q}} + (\mathbf{U}^T\mathbf{K}\mathbf{U})\mathbf{q} = \mathbf{U}^T\mathbf{f}_{ext}
$$
This is where the magic happens. Due to the properties of the generalized eigenvalue problem, the eigenvectors can be scaled to be **mass-orthonormal**, meaning $\mathbf{\psi}_i^T\mathbf{M}\mathbf{\psi}_j = \delta_{ij}$ (1 if $i=j$, 0 otherwise). This simplifies the projected matrices dramatically:
*   $\mathbf{U}^T\mathbf{M}\mathbf{U} = \mathbf{I}$ 
*   $\mathbf{U}^T\mathbf{K}\mathbf{U} = \mathbf{\Lambda}$, where $\mathbf{\Lambda}$ is a diagonal matrix of the eigenvalues, $\text{diag}(\lambda_1, \dots, \lambda_r)$.

If we assume **Rayleigh damping**, where $\mathbf{D} = \alpha\mathbf{M} + \beta\mathbf{K}$, the projected damping matrix also becomes diagonal: 
$$\mathbf{U}^T\mathbf{D}\mathbf{U} = \alpha\mathbf{I} + \beta\mathbf{\Lambda}.$$

This means that the final reduced system becomes a set of $r$ completely independent, 1D ordinary differential equations:
$$
\ddot{q}_i + (\alpha + \beta\lambda_i)\dot{q}_i + \lambda_i q_i = \mathbf{\psi}_i^T \mathbf{f}_{ext} \quad \text{for } i=1, \dots, r
$$


{{met}}{met:lec10:linear_modal_simulation}[Linear Modal Simulation]
We can think of the overall process as being split into a one-time precomputation and an efficient runtime loop.

**Precomputation:**
1.  Discretize the object to form the global mass matrix $\mathbf{M}$ and stiffness matrix $\mathbf{K}$.
2.  Solve the generalized eigenvalue problem $\mathbf{K}\mathbf{\psi}_i = \lambda_i \mathbf{M}\mathbf{\psi}_i$ for the $r$ smallest non-zero eigenvalues $\lambda_i$ and corresponding eigenvectors $\mathbf{\psi}_i$.
3.  Assemble the mass-orthonormal basis matrix $\mathbf{U} = [\mathbf{\psi}_1, \dots, \mathbf{\psi}_r]$.

**Runtime Loop (per time step):**
1.  Compute external forces $\mathbf{f}_{ext}$ in the full-space (e.g., from gravity, user interaction).
2.  Project the forces onto the reduced basis: $\mathbf{f}_{reduced} = \mathbf{U}^T\mathbf{f}_{ext}$.
3.  For each mode $i=1, \dots, r$, solve its simple 1D ODE ({{eqref:eq:lec10:reduced_linear_system}}) to update its amplitude $q_i$. This can be done with a simple and stable implicit integration scheme.
4.  Reconstruct the full-space deformation for rendering: $\mathbf{u} = \mathbf{U}\mathbf{q}$.

The computational savings are immense. Instead of solving a large, coupled $3n \times 3n$ system, we solve $r$ tiny, independent 1D equations, where $r$ might be 20-50 while $3n$ could be in the tens of thousands. 



