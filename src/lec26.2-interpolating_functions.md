## Interpolating Functions

In each time step of the Material Point Method (MPM), particles transfer their mass and momentum to the background grid, and later retrieve updated velocities from the grid to perform advection. Both transfers rely on **interpolating functions** to determine how each particle interacts with nearby grid nodes.

We define the interpolation function associated with grid node $\mathbf{i}$ as $N_\mathbf{i}(\mathbf{x})$. Here, $\mathbf{i}$ is a multi-index—$(i, j)$ in 2D or $(i, j, k)$ in 3D—and $\mathbf{x}$ is an arbitrary spatial position. When evaluating this function at a particle location $\mathbf{x}_p$, we use the shorthand:

$$
w_{\mathbf{i}p} = N_\mathbf{i}(\mathbf{x}_p)
$$

This weight determines how strongly particle $p$ influences grid node $\mathbf{i}$: the closer the particle is to the node, the larger $w_{\mathbf{i}p}$ becomes.

> {{exp}}{exp:lec26:linear}[Linear Interpolation]
The simplest choice is the **linear (tent) function**:
$$
N(x) =
\begin{cases}
1 - |x| & \text{if } 0 \leq |x| < 1 \\\\
0 & \text{otherwise}
\end{cases}
$$
To apply this in a grid-based setting, we scale the spatial coordinates relative to the **grid spacing** $h$ (i.e., the distance between adjacent grid nodes along one axis). The scaled version becomes:
$$
N\left( \frac{x_p - x_i}{h} \right)
$$
In higher dimensions (2D, 3D), we use the **tensor product** of 1D functions:
$$
N_\mathbf{i}(\mathbf{x}_p) = N\left(\frac{x_p - x_i}{h}\right) \cdot N\left(\frac{y_p - y_j}{h}\right) \cdot N\left(\frac{z_p - z_k}{h}\right)
$$

Linear interpolation is computationally efficient and easy to implement, and is widely used in FLIP fluids. However, it suffers from two serious issues in MPM:

1. Its gradient $\nabla N(x)$ is **discontinuous**, which can cause unstable and noisy force evaluations.
2. The stencil is too narrow—**forces may be applied from distant grid nodes** where $w_{\mathbf{i}p}$ is near zero but $\nabla w_{\mathbf{i}p}$ is still large. This can result in numerical artifacts like **cell-crossing instability** or **numerical fracture**.

Because of these issues, linear interpolation is typically avoided in MPM, especially in solid simulation.

> {{exp}}{exp:lec26:quad}[Quadratic B-Spline Interpolation]
A better choice is the **quadratic B-spline**, which is $C^1$ continuous (smooth gradients) and has wider support:
$$
N(x) =
\begin{cases}
\frac{3}{4} - x^2 & \text{if } |x| < \frac{1}{2} \\\\
\frac{1}{2} \left( \frac{3}{2} - |x| \right)^2 & \text{if } \frac{1}{2} \leq |x| < \frac{3}{2} \\\\
0 & \text{otherwise}
\end{cases}
$$

Quadratic splines have a stencil of width $3h$ and provide smooth, stable interactions between particles and the grid. They are computationally efficient and require less memory than higher-order splines.

> {{exp}}{exp:lec26:cubic}[Cubic B-Spline Interpolation]
For even better smoothness and broader support, we can use the **cubic B-spline**:
$$
N(x) =
\begin{cases}
\frac{1}{2} |x|^3 - |x|^2 + \frac{2}{3} & \text{if } 0 \leq |x| < 1 \\\\
\frac{1}{6}(2 - |x|)^3 & \text{if } 1 \leq |x| < 2 \\\\
0 & \text{otherwise}
\end{cases}
$$

The cubic B-spline has support over $[-2h, 2h]$, making it more robust to numerical noise and deformation artifacts. It is especially useful when avoiding artificial fractures or instabilities is important. However, the wider support also means that each particle interacts with more grid nodes, which increases the **computational overhead during transfers** (e.g., mass, momentum, and force). This trade-off between smoothness and efficiency should be considered when choosing an interpolation kernel.

### Gradient of the Interpolating Function

Gradients are needed during force computation. For tensor product splines, we compute the gradient using the chain rule:

$$
\nabla N_\mathbf{i}(\mathbf{x}_p) =
\begin{pmatrix}
\frac{1}{h} N'\left(\frac{x_p - x_i}{h}\right) N\left(\frac{y_p - y_j}{h}\right) N\left(\frac{z_p - z_k}{h}\right) \\\\
N\left(\frac{x_p - x_i}{h}\right) \frac{1}{h} N'\left(\frac{y_p - y_j}{h}\right) N\left(\frac{z_p - z_k}{h}\right) \\\\
N\left(\frac{x_p - x_i}{h}\right) N\left(\frac{y_p - y_j}{h}\right) \frac{1}{h} N'\left(\frac{z_p - z_k}{h}\right)
\end{pmatrix}
$$

Here, $N'(x)$ is the derivative of the 1D basis function $N(x)$.