## SDF-Based Sphere Collider

In [Signed Distance](./lec7.1-signed_dists.md) section, we introduced **analytical representations** of solid geometries—where shapes like spheres, boxes, and half-spaces are defined using mathematical constraints on their coordinates. One powerful abstraction introduced there was the **Signed Distance Function (SDF)**. This function evaluates, at any given point in space, the signed distance to the surface of a geometry: negative values indicate points inside the object, positive values are outside, and zero lies exactly on the surface.

This concept translates naturally into **collision detection and boundary enforcement** in simulation frameworks like MPM.

### Representing a Collider with Analytical SDF

Consider a **2D sphere** (circle) with center $\mathbf{c} = (c_x, c_y)$ and radius $r$. Its SDF is defined as:

$$
\phi(\mathbf{x}) = \|\mathbf{x} - \mathbf{c}\| - r
$$

- If $\phi(\mathbf{x}) < 0$, the point is **inside** the sphere.
- If $\phi(\mathbf{x}) = 0$, the point is **on** the sphere’s surface.
- If $\phi(\mathbf{x}) > 0$, the point is **outside** the sphere.

This definition allows us to apply **contact boundary conditions** uniformly across the simulation domain by evaluating the SDF at each grid node.

{{imp}}{imp:lec29:sphere_sdf}[SDF-Based Sphere Collider as Grid Boundary Condition]
```python
# Grid Update (Section 26.3)
def update_grid():
    for i, j in grid_m:
        if grid_m[i, j] > 0:
            grid_v[i, j] = grid_v[i, j] / grid_m[i, j] # extract updated nodal velocity from transferred nodal momentum
            grid_v[i, j] += ti.Vector([0, -9.81]) * dt # gravity

            # Dirichlet BC near the bounding box
            if i <= 3 or i > grid_size - 3 or j <= 2 or j > grid_size - 3:
                grid_v[i, j] = 0
            
            x_i = (ti.Vector([i, j])) * dx # position of the grid-node
            
            # a sphere SDF as boundary condition
            sphere_center = ti.Vector([0.5, 0.5])
            sphere_radius = 0.05 + dx # add a dx-gap to avoid penetration
            if (x_i - sphere_center).norm() < sphere_radius:
                normal = (x_i - sphere_center).normalized()
                diff_vel = -grid_v[i, j]
                dotnv = normal.dot(diff_vel)
                dotnv_frac = dotnv * (1.0 - sdf_friction)
                grid_v[i, j] += diff_vel * sdf_friction + normal * dotnv_frac
```
