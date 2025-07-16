## Constraint Formulation

The main loop, described in the previous chapter, relies on the solver to correct the predicted particle positions $\mathbf{p}$ so that they satisfy all defined constraints. We will begin by framing the problem as a large, non-linear system of equations and inequalities. Then, we will derive the core mathematical machinery used to solve this system, which involves linearizing the constraint functions and applying a Gauss-Seidel-type iterative scheme. The derivation will show how to compute the position correction for a single constraint while accounting for particle masses.


### Problem Formulation

The central task of the solver is to find a set of position corrections $\Delta\bm{p}_i$ that move the predicted positions $\bm{p}_i$ to a new state $\bm{p}_i + \Delta\bm{p}_i$ where all $M$ constraints are satisfied. If we concatenate all particle positions into a single state vector $\bm{p} = [\bm{p}_1^T, ..., \bm{p}_N^T]^T \in \mathbb{R}^{3N}$, the problem is to solve the following system of mixed equalities and inequalities:
$$
{{numeq}}{eq:solver:system}
\begin{aligned}
C_1(\bm{p}) &\succ 0 \\
C_2(\bm{p}) &\succ 0 \\
\vdots \\
C_M(\bm{p}) &\succ 0
\end{aligned}
$$

where each $C_j(\bm{p})$ may represent an equality ($=0$) or an inequality ($\ge 0$). This system presents a significant challenge. The problem comprises of a set of $M$
equations for $3N$ unknown position components. If $M \geq 3N$, then the system is over-determined, else if $M ≤3N$, then the system is under-determined. Additionally, the system is typically non-linear, as constraint functions often involve distances or angles. Solving such a system globally and simultaneously is computationally intractable for real-time applications.

PBD circumvents this complexity by adopting a local and iterative approach. Instead of solving the entire system at once, it processes one constraint at a time in a sequential manner, similar to the Gauss-Seidel method for solving linear systems. Each constraint is solved independently, and the position updates are applied immediately, influencing the solution of subsequent constraints in the same iteration. We repeatedly iterate through all the constraints and project the particles to valid locations with respect to the given constraint alone.