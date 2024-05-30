# Strong and Weak Forms

The update rules (refer to Equation {{eqref: eq:lec1:updateRule_implicit}}) and the corresponding optimization problems (refer to Equation {{eqref: eq:lec2:optimization_IE}}) utilized in solids simulation are derived by discretizing the conservation laws—our governing equations—from their continuous forms. This chapter will explore the derivation of both the strong and weak forms of these conservation laws. We will then discuss the methods for their temporal and spatial discretizations, which are essential for formulating the discrete problems we aim to solve.

The fundamental governing equations central to our study are the conservation of mass and the conservation of momentum (Newton's Second Law). We will outline these equations below and provide detailed derivations later in this lecture.

> {{def}}{def:lec16:strong_form}[Strong Form]
> Letting $\mathbf{V}(\mathbf{X},t)=\frac{\partial\phi(\mathbf{X},t)}{\partial t}=\frac{\partial\mathbf{x}(\mathbf{X},t)}{\partial t}$ be the velocity defined over $\mathbf{X}$, the equations are {{#cite gonzalez2008first}}:
$$
\begin{align}
    R(\mathbf{X},t)J(\mathbf{X},t) = R(\mathbf{X},0) \quad &\text{Conservation of mass},\\
    R(\mathbf{X},0)\frac{\partial\mathbf{V}}{\partial t}(\mathbf{X}, t) = \nabla^{\mathbf{X}}\cdot\mathbf{P}(\mathbf{X}, t) + R(\mathbf{X},0)\mathbf{g}
    \quad &\text{Conservation of momentum},
\end{align}
$$
> where $\mathbf{X}\in\Omega_0$ and $t\geq 0$. Here $R$ is the mass density, $J(\mathbf{X}, t) = \det{\mathbf{F}(\mathbf{X}, t)}$, $\mathbf{P}$ is the first Piola-Kirchoff stress, and $\mathbf{g}$ is the constant gravitational acceleration. Note that $J(\mathbf{X}, 0) = 1$, and the mass conservation can also be written as
$$
\frac{\partial}{\partial t}\left(R(\mathbf{X},t)J(\mathbf{X},t)\right) = 0.
$$

These equations are initially presented in their strong form. In this lecture, we will also derive the equivalent weak form of the force balance equation (conservation of momentum). The weak form reformulates the conservation law using integral expressions, which are crucial for the subsequent derivation of the temporal and spatial discretizations of the equations using the Finite Element Method.


<!-- <p style="color:red">TODO: Discuss why not directly discretize the strong form?</p> -->
<!-- Remark in 16.3 -->