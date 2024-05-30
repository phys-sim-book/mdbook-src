## Point-Edge Distance

Next, we calculate the point-edge distance and its derivatives. These will be used to solve for the contact forces. For a node $\mathbf{p}$ and an edge $\mathbf{e}_0 \mathbf{e}_1$, their squared distance is defined as

$$
d^{\text{PE}}_{\text{sq}}(\mathbf{p}, \mathbf{e}_0, \mathbf{e}_1) =  \min_\lambda \|\mathbf{p} - ((1 - \lambda)\mathbf{e}_0 +  \lambda \mathbf{e}_1)\|^2 \quad \text{s.t.} \quad \lambda \in [0,1],
$$

which is the closest squared distance between $\mathbf{p}$ and any point on $\mathbf{e}_0 \mathbf{e}_1$.

> **{{rem}}{rem:lec21:dist_calc}[Distance Calculation Optimization]**
> Here, we use the squared unsigned distances for evaluating the contact energies. This approach avoids taking square roots, which can complicate the expression of the derivatives and increase numerical rounding errors during computation. Additionally, unsigned distances can be directly extended for codimensional pairs, such as point-point pairs, which are useful when simulating particle contacts in 2D. They also do not suffer from locking issues, as signed distances do, when there are large displacements.

Fortunately, $d^{\text{PE}}_{\text{sq}}(\mathbf{p}, \mathbf{e}_0, \mathbf{e}_1)$ is a piece-wise smooth function w.r.t. the DOFs:
$$
d^{\text{PE}}_{\text{sq}}(\mathbf{p}, \mathbf{e}_0, \mathbf{e}_1) = 
\begin{cases}
    \|\mathbf{p} - \mathbf{e}_0\|^2  \quad \text{if} \ (\mathbf{e}_1 - \mathbf{e}_0) \cdot (\mathbf{p} - \mathbf{e}_0) < 0, \\
    \|\mathbf{p} - \mathbf{e}_1\|^2  \quad \text{if} \ (\mathbf{e}_1 - \mathbf{e}_0) \cdot (\mathbf{p} - \mathbf{e}_0) > \| \mathbf{e}_1 - \mathbf{e}_0 \|^2, \\
    \frac{1}{\|\mathbf{e}_1 - \mathbf{e}_0\|^2} \big(\det([\mathbf{p} - \mathbf{e}_0, \mathbf{e}_1 - \mathbf{e}_0])\big)^2  \quad \text{otherwise,}
\end{cases}
{{numeq}}{eq:lec21:pw_pe_dist}
$$
where the smooth expression can be determined by checking whether the node is inside the orthogonal span of the edge. Given these smooth expressions, we can differentiate each of them and obtain the derivatives of the point-edge distance function. The implementations are as follows:

{{imp}}{imp:lec21:PointEdgeDistance}[Point-Edge distance calculation (Hessian omitted), PointEdgeDistance.py]
```python
{{#include solid-sim-tutorial/7_self_contact/distance/PointEdgeDistance.py:PE_val_grad}}
```
It can be verified that the point-edge distance function is $C^1$-continuous everywhere, including at the interfaces between different segments. For the point-point case, we have:

{{imp}}{imp:lec21:PointPointDistance}[Point-Point distance calculation, PointPointDistance.py]
```python
{{#include solid-sim-tutorial/7_self_contact/distance/PointPointDistance.py}}
```

For the point-line case, the distance evaluations can be implemented as follows, and the derivatives can be obtained using symbolic differentiation tools.

{{imp}}{imp:lec21:PointLineDistance}[Point-Line distance calculation (Hessian omitted), PointLineDistance.py]
```python
{{#include solid-sim-tutorial/7_self_contact/distance/PointLineDistance.py:PL_val_grad}}
```
