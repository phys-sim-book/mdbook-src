## Solution Accuracy

So why can we solve Equation {{eqref: eq:lec7:barrier_IPC}} to approximate the solution of the original problem in Equation {{eqref: eq:lec7:constrained_IP}}?
Similar to [Dirichlet Boundary Conditions](lec2.2-dirichlet_BC.md), at the solution \\(x^*\\) of Equation {{eqref: eq:lec7:constrained_IP}}, the following KKT conditions all hold:
$$
\begin{aligned}
    \nabla E(x^*) - \sum_{i,j} \gamma_{ij} \nabla d_{ij}(x^*) = 0, \\
    \forall {i,j}, \ d_{ij}(x^*) \geq 0, \ \gamma_{ij} \geq 0, \ \gamma_{ij} d_{ij}(x^*) = 0.
\end{aligned}
{{numeq}}{eq:lec7:IC_KKT}
$$
While at the local optimum \\(x'\\) of Equation {{eqref: eq:lec7:barrier_IPC}}, we have
$$
    \nabla E(x') + h^2 \nabla P_b(x') = 0,
    {{numeq}}{eq:lec7:optimality_barrier}
$$
which is equivalently
$$
    \nabla E(x') + h^2 \sum_{i,j} A_i \hat{d} \nabla b(d_{ij}(x')) = 0
$$
and
$$
    \nabla E(x') + h^2 \sum_{i,j} A_i \hat{d} 
    \frac{\partial b}{\partial d}(d_{ij}(x')) \nabla d_{ij}(x') = 0
$$
if we plug in the expression of \\(\nabla b(d_{ij})\\).
Let \\(\gamma_{ij}' = -h^2 A_i \hat{d} \frac{\partial b}{\partial d}(d_{ij}(x'))\\), we can further rewrite Equation {{eqref: eq:lec7:optimality_barrier}} as
$$
    \nabla E(x') - \sum_{i,j} \gamma_{ij}' \nabla d_{ij}(x') = 0,
$$
which is essentially the stationarity condition (first line in Equation {{eqref: eq:lec7:IC_KKT}}) if we take \\(\gamma_{ij}'\\) as the dual variable.
Now since the barrier function provides arbitrarily large repulsion to avoid interpenetration, we know that \\(\forall i,j\\), \\(d_{ij}(x') \geq 0\\). In addition, \\(\gamma_{ij}' \geq 0\\) also holds for all \\(i,j\\) because \\(\frac{\partial b}{\partial d} \leq 0\\) by construction. This means that at \\(x'\\), we have momentum balance, no interpenetrations, and contact forces only push but not pull. 

In our simulation, the only Karush-Kuhn-Tucker (KKT) condition not strictly satisfied at \\( x' \\) is the complementarity slackness condition. This arises due to the way our barrier approximation functions. Specifically, we have a situation where \\( \gamma_{ij} > 0  \Longleftrightarrow  0 < d_{ij} < \hat{d} \\), representing the activation of contact forces based on the distance between solids and obstacles.

As the threshold \\( \hat{d} \\) decreases, contact forces become active only when the solids are in closer proximity (as illustrated in {{ref: fig:lec7:barrier_func}}). This adjustment leads to a reduction in the complementarity slackness error, which can be controlled to a certain extent. However, it's important to note that this control comes at a cost: computational efficiency may be reduced. This is because sharper objective functions, resulting from smaller \\( \hat{d} \\) values, tend to require more Newton iterations to resolve. Therefore, there is a trade-off between the accuracy of the simulation (in terms of adhering to the KKT condition) and the computational resources required.