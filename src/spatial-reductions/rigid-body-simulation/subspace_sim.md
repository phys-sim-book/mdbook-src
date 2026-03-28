## Subspace Simulation

In this subsection, we derive a constrained version of the rigid body dynamics equations using a subspace optimization approach. This formulation connects rigid body dynamics to the Incremental Potential Contact (IPC) method, as will be further elaborated in subsequent sections.

Recall that in IPC, the simulation problem is posed as the following optimization:
$$
\begin{equation}
\begin{aligned}
    \mathbf{x}^{n+1} &= \argmin_{\mathbf{x}} E(\mathbf{x})  \nonumber \\ 
    \text{where} \ E(\mathbf{x}) &= \frac{1}{2} \Vert \mathbf{x} - \tilde{\mathbf{x}}^n\Vert + \Delta t^2P(\mathbf{x}). 
\end{aligned}
\end{equation}
$$

Here, $E(\mathbf{x})$ is the incremental potential, $\mathbf{x}$ denotes the particle position to be solved.


Let $\bm{x}$ be a particle position on a rigid body, parameterized by the body's rotation matrix $\bm{Q}$ and translation $\bm{q}$. The rigid body kinematics impose the constraint $\bm{x} = \bm{Q} \bar{\bm{x}} + \bm{q}$, where $\bar{\bm{x}}$ is the particle's position in the body-local frame. In the stacked form, this becomes:

$$
\mathbf{x} = \bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q},
$$

where $\bar{\mathbf{x}}$ and $\bar{\mathbf{S}}$ are constant matrices independent of $\mathbf{Q}$ and $\mathbf{q}$ as they only rely on the local particle positions in the body frame.

Substituting into the IPC formulation, we obtain the constrained optimization problem:

$$
\begin{equation}
\begin{aligned}
    \mathbf{Q}^{n+1}, \mathbf{q}^{n+1} &= \argmin_{\mathbf{Q}, \mathbf{q}} E(\mathbf{Q}, \mathbf{q}) \quad \text{s.t.} \quad \mathbf{f}(\mathbf{Q}) = \mathbf{0},  \nonumber\\ 
    \text{where} \quad E(\mathbf{Q}, \mathbf{q}) &= \frac{1}{2} \left\| \bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q} - \tilde{\mathbf{x}}^n \right\|^2 + \Delta t^2 P(\bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q}),
\end{aligned}
\end{equation}
$$

where the constraint $\mathbf{f}(\mathbf{Q}) = \bm{0}$ enforces rigidity of the rotation matrices $\bm{Q}_i$ for each rigid body $i$ i.e. $\bm{Q}_{i}^T\bm{Q}_{i} - \mathbb{I}_3 = \bm{0}$. 

Applying the method of Lagrange multipliers, we introduce the Lagrangian:

$$
G = E(\mathbf{Q}, \mathbf{q}) + \bm{\lambda}^\top \mathbf{f}(\mathbf{Q}),
$$

and derive the stationarity conditions:
$$
\begin{equation}
\begin{aligned}
\nabla_{\mathbf{q}} G &= 0,  \nonumber\\ 
\nabla_{\mathbf{Q}} G &= 0, \\ 
\nabla_{\bm{\lambda}} G &= 0.
\end{aligned}
\end{equation}
$$

This leads to the following constrained rigid body dynamics equations:

$$
\begin{equation}
\begin{aligned}
\bar{\mathbf{x}}^\top \mathbf{M} (\bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q}) + \Delta t^2 \bar{\mathbf{x}}^\top \nabla P(\bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q}) + (\nabla \mathbf{f}(\mathbf{Q}))^\top \bm{\lambda} &= \mathbf{0},  \nonumber\\
\bar{\mathbf{S}}^\top \mathbf{M} (\bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q}) + \Delta t^2 \bar{\mathbf{S}}^\top \nabla P(\bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q}) &= \mathbf{0}, \\
\mathbf{f}(\mathbf{Q}) &= \mathbf{0}.
\end{aligned}
\end{equation}
{{numeq}}{eq:lec25:rigid_body_constrained_formulation}
$$

These equations define the motion of rigid bodies under subspace-constrained dynamics, while satisfying both energy consistency and rigidity constraints.

