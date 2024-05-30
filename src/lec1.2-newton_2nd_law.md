## Newton's Second Law

Having defined a method for representing a solid geometry at a single instance in time, we now face the challenge of predicting the solid's motion and deformation over time. This prediction is a key component for accurate simulation.

Newton's second law, expressed as \\(\mathbf{f} = m \mathbf{a}\\), indicates that forces \\(\mathbf{f}\\) are the primary reasons for changes in velocity, as indicated by acceleration \\(\mathbf{a}\\). It's important to understand that when a solid's displacement fields extend beyond simple translational or rotational movements, or a linear combination thereof, it indicates deformation. By applying Newton's second law to each sample point, we can effectively predict the movement and deformation of solids. This concept is concisely represented in vector form:

$$
    \begin{aligned}
    \frac{d x}{d t} & = v, \\
    M\frac{d v}{d t} & = f. 
    \end{aligned}
    {{numeq}}{eq:lec1:governing_discSpace_contTime}
$$

In this representation, \\(M\in\mathbb{R}^{dn\times dn}\\) is the mass matrix, and \\(x\\), \\(v\\), and \\(f\\) are the column vectors stacking position, velocity, and force, respectively. This approach lays the groundwork for our simulations of deformable solids, integrating principles of motion in both discrete space and continuous time.

> **{{rem}}{rem:lec2:stacked_var}[Stacked Variables]** Though the mass matrix \\(M\\) isn't necessarily a diagonal matrix in theory, it's often simplified to one in practical applications. This results in a _lumped_ mass matrix, representing a system of discrete point masses and offering an efficient way to handle complex systems.
Consider a two-point system in two dimensions to illustrate this. The lumped mass matrix for such a system is represented as:
\\[
M = \begin{pmatrix}
m_1 &    &    &   \\\\
    & m_1 &    &   \\\\
    &    & m_2 &   \\\\
    &    &    & m_2
\end{pmatrix},
\\]
Here, we assume vectors like \\({v}\\) (as well as \\({x}\\) and \\({f}\\)) are stacked in a specific order:
\\[
v = (v_{11}, v_{12}, v_{21}, v_{22})^T,
\\]
where \\(v_{i\alpha}\\) denotes the \\(\alpha\\)th component of the velocity \\(\mathbf{v}_i\\) for the \\(i\\)th point. Such an organized structure simplifies calculations significantly and enhances the understanding of the system's dynamics.


