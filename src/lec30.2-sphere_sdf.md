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

{{imp}}{imp:lec30:sphere_sdf}[Sphere SDF Collider with Frictional Contact, simulator.py]
```python
{{#include solid-sim-tutorial/11_mpm_sand/simulator.py:sphere_sdf}}
```