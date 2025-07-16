## Rigid Body Dynamics

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
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho \bm{x} \times \bm{v} \, d\bm{x}\right) = \int_{\mathcal{B}} \rho \bm{x} \times \bm{f}(\bm{x}) \, d\bm{x},
$$
where $\rho = \rho(\bm{x})$ is the mass density of $\mathcal{B}$ at position $\bm{x}$.

Now let $\bm{c}$ denote the center of mass (COM) of $\mathcal{B}$. Then:

$$
\begin{aligned}
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho \bm{c} \times \bm{v} \, d\bm{x}\right) 
&= \int_{\mathcal{B}} \rho \bm{c} \times \frac{d\bm{v}}{dt} \, d\bm{x} \\
&= \int_{\mathcal{B}} \rho \bm{c} \times \bm{f}(\bm{x}) \, d\bm{x}.
\end{aligned}
$$

Combining both:

$$
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{v} \, d\bm{x}\right) = \int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{f}(\bm{x}) \, d\bm{x}.
$$

Since $\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{v}_{\bm{c}} d\bm{x} = (\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) d\bm{x}) \times \bm{v}_{\bm{c}} = \bm{0} \times \bm{v}_{\bm{c}} = \bm{0}$, we can further simplify:

$$
\frac{d}{dt}\left(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times (\bm{v} - \bm{v}_{\bm{c}}) \, d\bm{x}\right) = \int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times \bm{f}(\bm{x}) \, d\bm{x},
$$
where the right-hand side is the torque $\bm{\tau}$ about the center of mass.

Denoting the right-hand side as the torque $\bm{\tau}_{\bm{c}}$, compute the left-hand side:

$$
    \begin{aligned}
        \frac{d}{dt}(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times (\bm{v} - \bm{v}_{\bm{C}}) d\bm{x}) &= \frac{d}{dt}(\int_{\mathcal{B}} \rho (\bm{x} - \bm{c}) \times (\bm{\omega} \times (\bm{x} - \bm{c})) d\bm{x}) \\ 
        &= \frac{d}{dt}(\int_{\mathcal{B}} \rho(\langle \bm{x} - \bm{c}, \bm{x} - \bm{c}\rangle \bm{\omega} - \langle \bm{x} - \bm{c},\bm{\omega}\rangle (\bm{x} - \bm{c}) )d\bm{x}) \\
        &= \mathbf{I}_{\mathcal{B}}^{\bm{c}} \bm{\omega}.
    \end{aligned}
$$

Here, the **inertia tensor** $\mathbf{I}_{\mathcal{B}}^{\bm{c}}$ is defined as:

$$
\mathbf{I}_{\mathcal{B}}^{\bm{c}} := \int_{\mathcal{B}} \rho \left(\Vert\bm{x} - \bm{c}\Vert_2^2 \mathbb{I} - (\bm{x} - \bm{c})(\bm{x} - \bm{c})^T \right) d\bm{x}.
$$

Since $\bm{x}$ depends on $t$, so does $\mathbf{I}_{\mathcal{B}}^{\bm{c}}$. Letting $\mathbf{R}(t)$ be the rotation matrix (the rotation of $\mathcal{B}$ from time $0$ to $t$). It's easy to verify that:

$$
\mathbf{I}_{\mathcal{B}}^{\bm{c}}(t) = \mathbf{R}(t)\mathbf{I}_{\mathcal{B}}^{\bm{c}}(0)\mathbf{R}(t)^T.
$$

Differentiating yields:

$$
\bm{\tau}_{\bm{c}} = \bm{\omega} \times (\mathbf{I}^{\bm{c}} \bm{\omega}) + \mathbf{I}^{\bm{c}} \frac{d\bm{\omega}}{dt}.
$$

Thus, the rigid body dynamics law becomes:

$$
\begin{aligned}
\frac{d}{dt}(m\bm{v}_{\bm{c}}) &= \bm{f}, \\
\bm{\omega} \times (\mathbf{I}^{\bm{c}} \bm{\omega}) + \mathbf{I}^{\bm{c}} \frac{d\bm{\omega}}{dt} &= \bm{\tau}_{\bm{c}}.
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
\bm{q}_{n+1} &= \frac{\hat{\bm{q}}_{n+1}}{|\hat{\bm{q}}_{n+1}|}.
\end{aligned}
{{numeq}}{eq:lec25:rigid_body_explicit}
$$

Here, $\bm{q}$ is the unit quaternion representing the body’s rotation. Its update is based on a first-order approximation:

$$
\begin{aligned}
    \bm{\widetilde{q}} - \bm{q} &= e^{\frac{\Delta t}{2}\bm{\omega}} \bm{q}  - \bm{q}  \nonumber\\ 
    &= (\cos\frac{\Vert\bm{\omega}\Vert\Delta t}{2} - 1 + \frac{\bm{\omega}}{\Vert\bm{\omega}\Vert}\sin\frac{\Vert\bm{\omega}\Vert\Delta t}{2})\bm{q} \\ 
    &= (\Theta(\Delta t^2) + \frac{\Delta t}{2}\bm{\omega})\bm{q},
\end{aligned}
$$
which leads to: 
$$
\lim_{\Delta t \to 0} \frac{\bm{\widetilde{q}} - \bm{q}}{\Delta t} = \frac{1}{2} \bm{w} \bm{q},
$$

where $\bm{w} = (\bm{\omega}_x, \bm{\omega}_y, \bm{\omega}_z, 0)$; $\bm{\widetilde{q}}$ is the composition of the rotation represented by the rotation vector $\Delta t\bm{\omega}$ and the rotation represented by $\bm{q}$.

> **{{rem}}{rem:lec25:quaternion}[Rotation Vector to Quaternion]**
A rotation of angle $\theta$ around the axis defined by the unit vector $\bm{u} = (\bm{u}_x, \bm{u}_y, \bm{u}_z) = \bm{u}_x \bm{i} + \bm{u}_y \bm{j} + \bm{u}_z \bm{k}$ can be represented by the quaternion
$$
\bm{q} = e^{\frac{\theta}{2}(\bm{u}_x \bm{i} + \bm{u}_y \bm{j} + \bm{u}_z \bm{k})} = \cos{\frac{\theta}{2}} + (\bm{u}_x \bm{i} + \bm{u}_y \bm{j} + \bm{u}_z \bm{k}) \sin{\frac{\theta}{2}} = \cos{\frac{\theta}{2}} + \bm{u}\sin{\frac{\theta}{2}}
$$