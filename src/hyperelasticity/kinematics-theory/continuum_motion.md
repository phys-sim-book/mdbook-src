## Continuum Motion

Kinematics is the study of motion within continuum materials, focusing primarily on the changes in shape or deformation that occur, whether locally or globally, across different coordinate systems. The aim is to describe motion both qualitatively and quantitatively, which is crucial for deriving the governing equations of dynamics and mechanical responses. Notably, kinematics can often be described without the need to introduce concepts like force, stress, or even mass.

In continuum mechanics, deformation is typically represented through three key components:
- **Material (or undeformed) space $\mathbf{X}$**: This represents the initial position of any point in the material.
- **World (or deformed) space $\mathbf{x}$**: This indicates the current position of any point.
- **Deformation map $\phi(\mathbf{X},t)$**: This function maps points from the material space to the world space, showing how the position of material points changes over time.

At the initial time $t=0$, the material space $\mathbf{X}$ and the world space $\mathbf{x}$ coincide, meaning every point starts at its undeformed position.

> **{{def}}{def:lec12:flow_map}[Deformation/Flow Map]**
> The motion of material in continuum mechanics is determined by a mapping $\phi(\cdot,t):\Omega^0\rightarrow\Omega^t$, where $\Omega^0,\Omega^t\subset\mathbb{R}^d$ and $d=2$ or $3$ represents the dimension of the simulated problem (or domain). This mapping, often referred to as the flow map or the deformation map, is crucial in understanding how material points move over time.
>
> - **Material Points $\mathbf{X}$**: Points in the set $\Omega^0$ are known as material points and are designated as $\mathbf{X}$.
> - **Current Locations $\mathbf{x}$**: Points in $\Omega^t$ represent the location of material points at time $t$, and are referred to as $\mathbf{x}$.
> The deformation map $\phi$ describes the trajectory of each material point $\mathbf{X}\in\Omega^0$ throughout time, expressed as:
$$
    \mathbf{x} = \mathbf{x}(\mathbf{X},t) = \phi(\mathbf{X},t).
$$

> **{{exp}}{exp:lec12:motion_examples}**
> If our object is moving with a constant speed $v$ along direction $\mathbf{n}$, then we have
$$
\mathbf{x} = \mathbf{X} + tv\mathbf{n}. 
{{numeq}}{eq:lec12:motion_eg1}
$$
> If an object undergoes some rigid motion after time $t$ (compared to time $0$), we will have
$$
\mathbf{x} = \mathbf{R}\mathbf{X} + \mathbf{b}, 
{{numeq}}{eq:lec12:motion_eg2}
$$
> where $\mathbf{R}$ is a rotation matrix, and $\mathbf{b}$ is some translation. $\mathbf{R}$ and $\mathbf{b}$ will likely be functions of time $t$ and the initial position $\mathbf{X}$, depending on the actual motion.

The mapping $\phi$ can be used to quantify relevant continuum-based physics. For example, the velocity of a given material point $\mathbf{X}$ at time $t$ is
$$
\mathbf{V}(\mathbf{X},t) = \frac{\partial \phi}{\partial t}(\mathbf{X},t), {{numeq}}{eq:lec12:lagragian_V}
$$
and the acceleration is
$$
\mathbf{A}(\mathbf{X},t) = \frac{\partial^2 \phi}{\partial t^2}(\mathbf{X},t) = \frac{\partial \mathbf{V}}{\partial t}(\mathbf{X},t). {{numeq}}{eq:lec12:lagragian_A}
$$
That is, $\mathbf{V}(\cdot,t):\Omega^0\rightarrow\mathbb{R}^d$ and $\mathbf{A}(\cdot,t):\Omega^0\rightarrow\mathbb{R}^d$.

> **{{rem}}{rm:lec12:lagrangian_view}**
> In the above, the velocity $\mathbf{V}$ and acceleration $\mathbf{A}$ are defined from the Lagrangian perspective. This means that both velocity and acceleration are functions of the material configuration $\mathbf{X}$ and time $t$, focusing on specific particles within the material. Physically, this implies that these measurements pertain to particles that have their own mass and have occupied some volume from the beginning of the simulation. The Lagrangian view is particularly valuable for tracking individual particle dynamics over time, offering detailed insights into how particles move, accelerate, and interact within the material under various conditions.
