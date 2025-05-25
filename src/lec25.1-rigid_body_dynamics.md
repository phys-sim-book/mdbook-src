# Rigid Body Dynamics
## Unconstrained Version 
Before jumping into ABD, let's review the traditional approach to simulate rigid body dynamics. 

Let’s begin by reviewing Newtonian mechanics — specifically, Newton’s second law for a particle at position $\bm{x}(t)$ with mass $m$:

$$
\begin{aligned}
\frac{d \bm{x}}{d t} & = \bm{v}, \\
m\frac{d \bm{v}}{d t} & = \bm{f}.
\end{aligned}
$$

Now, compute $\frac{d (m \bm{x} \times \bm{v})}{dt}$:

$$
\begin{aligned}
\frac{d (m \bm{x} \times \bm{v})}{dt}
&= m\frac{d\bm{x}}{dt} \times \bm{v} + m\bm{x} \times \frac{d\bm{v}}{dt} \\
&= m\bm{v} \times \bm{v} + \bm{x} \times \bm{f} \\
&= \bm{0} + \bm{x} \times \bm{f} \\
&= \bm{x} \times \bm{f}.
\end{aligned}
$$

Here, $m\bm{x} \times \bm{v}$ is called the **angular momentum**, and $\bm{x} \times \bm{f}$ is the **torque**.

For a rigid body $\mathcal{B}$, we can integrate over its volume:

$$
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho \bm{x} \times \bm{v} \, d\bm{x}\right) = \int_{\mathcal{B}} \rho \bm{x} \times \bm{f}(\bm{x}) \, d\bm{x}
$$
, where $\rho = \rho(\bm{x})$ is the mass density of $\mathcal{B}$ at position $\bm{x}$.

Now let $\bm{c}$ denote the center of mass (COM) of $\mathcal{B}$. Then:

$$
\begin{aligned}
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho \bm{c} \times \bm{v} \, d\bm{x}\right) 
&= \int_{\mathcal{B}} \rho \bm{c} \times \frac{d\bm{v}}{dt} \, d\bm{x} \\
&= \int_{\mathcal{B}} \rho \bm{c} \times \bm{f}(\bm{x}) \, d\bm{x}
\end{aligned}
$$

Combining both:

$$
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{v} \, d\bm{x}\right) = \int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{f}(\bm{x}) \, d\bm{x}
$$

Since $\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{v}_{\bm{c}} d\bm{x} = (\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) d\bm{x}) \times \bm{v}_{\bm{c}} = \bm{0} \times \bm{v}_{\bm{c}} = \bm{0}$, we can further simplify:

$$
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times (\bm{v} - \bm{v}_{\bm{c}}) \, d\bm{x}\right) = \int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{f}(\bm{x}) \, d\bm{x}
$$
where the right-hand side is the torque $\bm{\tau}$ about the center of mass.

Denoting the right-hand side as the torque $\bm{\tau}_{\bm{c}}$, compute the left-hand side:

$$
    \begin{aligned}
        \frac{d}{dt}(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times (\bm{v} - \bm{v}_{\bm{C}}) d\bm{x}) &= \frac{d}{dt}(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times (\bm{\omega} \times (\bm{x} - \bm{c})) d\bm{x}) \\ 
        &= \frac{d}{dt}(\int_{\mathcal{B}} \rho(\langle \bm{x} - \bm{c}, \bm{x} - \bm{c}\rangle \bm{\omega} - \langle \bm{x} - \bm{c},\bm{\omega}\rangle (\bm{x} - \bm{c}) )d\bm{x}) \\
        &= \mathbf{I}_{\mathcal{B}}^{\bm{c}} \bm{\omega}
    \end{aligned}
$$

Here, the **inertia tensor** $\mathbf{I}_{\mathcal{B}}^{\bm{c}}$ is defined as:

$$
\mathbf{I}_{\mathcal{B}}^{\bm{c}} := \int_{\mathcal{B}} \rho \left(\Vert\bm{x} - \bm{c}\Vert_2^2 \mathbb{I} - (\bm{x} - \bm{c})(\bm{x} - \bm{c})^T \right) d\bm{x}
$$

Since $\bm{x}$ depends on $t$, so does $\mathbf{I}_{\mathcal{B}}^{\bm{c}}$. Letting $\mathbf{R}(t)$ be the rotation matrix (the rotation of $\mathcal{B}$ from time $0$ to $t$). It's easy to verify that:

$$
\mathbf{I}_{\mathcal{B}}^{\bm{c}}(t) = \mathbf{R}(t)\mathbf{I}_{\mathcal{B}}^{\bm{c}}(0)\mathbf{R}(t)^T
$$

Differentiating yields:

$$
\bm{\tau}_{\bm{c}} = \bm{\omega} \times (\mathbf{I}^{\bm{c}} \bm{\omega}) + \mathbf{I}^{\bm{c}} \frac{d\bm{\omega}}{dt}
$$

Thus, the rigid body dynamics law becomes:

$$
\begin{aligned}
\frac{d}{dt}(m\bm{v}_{\bm{c}}) &= \bm{f}, \\
\bm{\omega} \times (\mathbf{I}^{\bm{c}} \bm{\omega}) + \mathbf{I}^{\bm{c}} \frac{d\bm{\omega}}{dt} &= \bm{\tau}_{\bm{c}}
\end{aligned}
{{numeq}}{eq:lec25:rigid_body_dynamics}
$$

These equations resemble Newton's second law. The first describes how forces affect the linear momentum of the rigid body, while the second describes how torques influence its angular momentum.

With a simple explicit integrator, we can simulate rigid body motion via:

$$
\begin{aligned}
\bm{v}_{n+1} &= \bm{v}_n + \Delta t \frac{\bm{f}_n}{m}, \\
\bm{x}_{n+1} &= \bm{x}_n + \Delta t \bm{v}_n, \\
\bm{\omega}_{n+1} &= \bm{\omega}_n + \Delta t (\mathbf{I}^{\bm{c}})^{-1}(\bm{\tau}_{\bm{c}} - \bm{\omega} \times (\mathbf{I}^{\bm{c}} \bm{\omega})), \\
\hat{\bm{q}}_{n+1} &= \bm{q}_n + \frac{\Delta t}{2} \bm{\omega} \bm{q}_n, \\
\bm{q}_{n+1} &= \frac{\hat{\bm{q}}_{n+1}}{|\hat{\bm{q}}_{n+1}|}
\end{aligned}
{{numeq}}{eq:lec25:rigid_body_explicit}
$$

Here, $\bm{q}$ is the unit quaternion representing the body’s rotation. Its update is based on a first-order approximation:

$$
\begin{equation}
\begin{aligned}
    \bm{\tilde{q}} - \bm{q} &= e^{\frac{\Delta t}{2}\bm{\omega}}  - \bm{q}  \nonumber\\ 
    &= (\cos\frac{\Vert\bm{\omega}\Vert\Delta t}{2} - 1 + \frac{\bm{\omega}}{\Vert\bm{\omega}\Vert}\sin\frac{\Vert\bm{\omega}\Vert\Delta t}{2})\bm{q} \\ 
    &= (\Theta(\Delta t^2) + \frac{\Delta t}{2}\bm{\omega})\bm{q}. 
\end{aligned}
\end{equation}
$$
, which leads to: 
$$
\lim_{\Delta t \to 0} \frac{\bm{\tilde{q}} - \bm{q}}{\Delta t} = \frac{1}{2} \bm{w} \bm{q}
$$

where $\bm{w} = (\bm{\omega}_x, \bm{\omega}_y, \bm{\omega}_z, 0)$; $\bm{\tilde{q}}$ is the composition of the rotation represented by the rotation vector $\Delta t\bm{\omega}$ and the rotation represented by $\bm{q}$.

## Constrained Version
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

where $E(\mathbf{x})$ is the IPC energy, $\mathbf{x}$ denotes the particle position to be solved.


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
    \text{where} \quad E(\mathbf{Q}, \mathbf{q}) &= \frac{1}{2} \left\| \bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q} - \tilde{\mathbf{x}}^n \right\|^2 + \Delta t^2 P(\bar{\mathbf{x}} \mathbf{Q} + \bar{\mathbf{S}} \mathbf{q}).
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
\nabla_{\mathbf{q}} G &= 0  \nonumber\\ 
\nabla_{\mathbf{Q}} G &= 0 \\ 
\nabla_{\bm{\lambda}} G &= 0
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

