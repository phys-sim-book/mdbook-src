Previously, we reviewed rigid body dynamics and implemented a simple solver using explicit integration.

When dealing with many rigid bodies — especially with complex geometry and dense contact — it’s natural to attempt IPC for contact resolution. However, representing a rigid body by its position $\bm{x}$ and rotation $\bm{q}$ creates a challenge: step size filtering in Newton’s method becomes nonlinear in $\alpha$, due to the nonlinearity of quaternion update.

Let the state of a rigid body be denoted by $\bm{y} = (\bm{x}, \bm{q})$, where $\bm{x}$ is the center of mass and $\bm{q}$ is a unit quaternion representing orientation. During a Newton iteration, let the update direction be $\bm{p} = (\bm{p}_{\bm{x}}, \bm{p}_{\bm{q}})$ with step size $\alpha$, so that the updated state is given by $\tilde{\bm{y}} = \bm{y} + \alpha \bm{p}$. The position of a vertex initially located at $\bm{x}_v^{(0)}$ is then transformed to:

$$
\tilde{\bm{x}}_v = \tilde{\bm{q}} (\bm{x}_v^{(0)} - \bm{x}^{(0)}) \tilde{\bm{q}}^{-1} + \tilde{\bm{x}}
$$

Expanding this shows $\tilde{\bm{x}}_v$ is nonlinear in $\alpha$:

$$
\tilde{\bm{x}}_v = (\bm{q} + \alpha \bm{p}_{\bm{q}})(\bm{x}_v^{(0)} - \bm{x}^{(0)})(\bm{q} + \alpha \bm{p}_{\bm{q}})^{-1} + \bm{x} + \alpha \bm{p}_{\bm{x}}
$$
and since $\tilde{\bm{q}}$ depends nonlinearly on $\alpha$, so does $\tilde{\bm{x}}_v$. Hence, linear CCD may fail to guarantee intersection-free motion.

We could model rigid bodies as very stiff soft bodies (e.g., mass-spring or hyperelastic), but this is inefficient and negates the DOF-reduction benefit.

Affine Body Dynamics (ABD) {{#cite lan2022affine}} addresses this: instead of strict rigidity, we allow tiny affine deformations $\bar{\bm{x}} \mapsto \bm{x}=\mathbf{A}\bm{x} + \bm{b}$ for each vertex $\bm{x}$ of the body where $\bar{\bm{x}}$ denotes the rest position. The body state is $\bm{y} = (\bm{b}^T, \bm{a}_1^T, \bm{a}_2^T, \bm{a}_3^T)$ where $\mathbf{A}=(\bm{a}_1, \bm{a}_2, \bm{a}_3)^T$, so the vertex position $\bm{x}$ becomes:

$$
\begin{equation}
\bm{x} = \mathbf{J} \bm{y} \nonumber
\end{equation}
$$

with $\mathbf{J}=(\mathbb{I}_{3\times 3}, \mathbb{I}_{3\times 3} \otimes \bar{\bm{x}}^T)$ where $\otimes$ denotes the Kronecker product.

This linearity makes $\bm{x}$ linearly dependent on the optimization step size $\alpha$, enabling robust linear CCD.

Let $E(\bm{x})$ be the augmented IPC energy. Original IPC solves:
$$
\begin{equation}
\nabla_{\bm{x}} E(\bm{x}) = 0 \nonumber
\end{equation}  
$$

ABD solves in a reduced subspace:

$$
\begin{equation}
\nabla_{\bm{y}} E(\mathbf{J} \bm{y}) = 0 \nonumber
\end{equation}
$$

Using the chain rule:

$$
\begin{aligned}
\nabla_{\bm{y}} E(\mathbf{J} \bm{y}) &= \mathbf{J}^T \nabla_{\bm{x}} E(\bm{x}) \\
D^2_{\bm{y}} E(\mathbf{J} \bm{y}) &= \mathbf{J}^T D^2_{\bm{x}} E(\bm{x}) \mathbf{J}

{{numeq}}{eq:lec25:newton_chain_rules}
\end{aligned}
$$
, we can solve Newton Optimization direction by solving the linear system $\mathbf{J}^T D^2_{\bm{x}} E(\bm{x}) \mathbf{J} = -\mathbf{J}^T \nabla_{\bm{x}} E(\bm{x})$.

With these, we can implement ABD using implicit Euler integration for a simple 2D case.

Before implementation, consider this geometric view: in 2D, an affine transformation is determined by 3 points. Choosing a triangle per rigid body, we can interpolate all vertices via barycentric coordinates. The triangle drives the simulation; the original mesh is used for collisions. This yields a reduced DOF system where the triangle controls motion—essentially what ABD does.

Now, let’s implement the simple 2D case:

```python
{{#include solid-sim-tutorial/9_reduced_DOF/simulator.py}}
```

Here we use a small stiffness parameter making the body softer to amplify the deformation of the affine body to better demonstrate the difference between ABD and IPC. The blue is the collision mesh, and the red one is the embedding mesh. 

<figure>
    <center>
        <img src="img/lec25/abd_sim.gif">
        <figcaption><b>{{fig}}{fig:lec25:abd_sim}</b> An illustration of ABD simulation.</figcaption>
    </center>
</figure>