## Complete Mesh Simulation

Now we combine the edge and volume constraints we've implemented to create a complete volumetric mesh simulation. The main simulation loop solves both constraint types iteratively, then updates velocities.

```python
def substep():
    """Complete mesh simulation integrating all components"""
    pre_solve(sdt, 1)
    for _ in range(solver_iterations):
        solve_edges(edge_compliance, sdt)
        solve_volumes(vol_compliance, sdt)
    post_solve(sdt)
```

The solver alternates between edge and volume constraints in each iteration. Edge constraints are solved first because they provide structural integrity, then volume constraints ensure incompressibility.

One of the key advantages of this implementation is the decoupling of simulation and visual meshes. We use a coarse tetrahedral mesh for efficient simulation and a fine triangular mesh for high-quality rendering:

```python
# Simulation mesh (tetrahedral)
pos = ti.Vector.field(3, dtype=ti.f64, shape=num_particles)
tet_ids = ti.field(ti.i32, shape=(num_tets, 4))
edge_ids = ti.field(ti.i32, shape=(num_edges, 2))

# Visual mesh (triangular)
vis_mesh_rest_pos = ti.Vector.field(3, dtype=ti.f64, shape=num_vis_verts)
vis_mesh_pos = ti.Vector.field(3, dtype=ti.f64, shape=num_vis_verts)
vis_mesh_indices = ti.field(ti.i32, shape=num_vis_tris * 3)
```

This separation allows for efficient simulation with coarse tetrahedra while maintaining high-quality rendering with fine triangles.

The visual mesh is deformed using barycentric coordinates computed from the simulation mesh:

```python
@ti.kernel
def update_vis_mesh():
    """Update visual mesh using barycentric skinning"""
    for i in range(num_vis_verts):
        tet_idx = skinning_info_tet_idx[i]
        if tet_idx < 0:
            vis_mesh_pos[i] = vis_mesh_rest_pos[i]
            continue
        
        p_indices = ti.Vector([tet_ids[tet_idx, 0], tet_ids[tet_idx, 1], tet_ids[tet_idx, 2], tet_ids[tet_idx, 3]])
        p0, p1, p2, p3 = pos[p_indices[0]], pos[p_indices[1]], pos[p_indices[2]], pos[p_indices[3]]
        b = skinning_info_bary_weights[i]
        b3 = 1.0 - b.sum()
        vis_mesh_pos[i] = b[0] * p0 + b[1] * p1 + b[2] * p2 + b3 * p3
```

This approach ensures that the visual mesh smoothly follows the deformation of the simulation mesh, providing high-quality rendering while maintaining simulation efficiency.

The skinning computation is accelerated using a spatial hash grid:

```python
def build_hash_grid():
    """Multi-step process to build the spatial hash grid for skinning acceleration"""
    grid_cell_counts.fill(0)
    
    @ti.kernel
    def count_verts_in_cells():
        for i in range(num_vis_verts):
            cell = get_grid_cell(vis_mesh_rest_pos[i])
            ti.atomic_add(grid_cell_counts[cell], 1)
    
    count_verts_in_cells()
    
    # Build prefix sum for cell starts
    total_verts = 0
    counts_np = grid_cell_counts.to_numpy()
    starts_np = np.zeros_like(counts_np)
    for i in range(GRID_SIZE):
        for j in range(GRID_SIZE):
            for k in range(GRID_SIZE):
                starts_np[i, j, k] = total_verts
                total_verts += counts_np[i, j, k]
    grid_cell_starts.from_numpy(starts_np)
```

This spatial acceleration structure allows for efficient neighbor finding during skinning computation, making the setup phase very fast even for complex meshes.

The simulation includes simple box collision handling:

```python
@ti.func
def clamp_to_box(p):
    """Clamp a position to stay within the collision box bounds"""
    return ti.Vector([
        ti.max(box_min[0], ti.min(box_max[0], p[0])),
        ti.max(box_min[1], ti.min(box_max[1], p[1])),
        ti.max(box_min[2], ti.min(box_max[2], p[2]))
    ])

@ti.kernel
def pre_solve(dt: ti.f64, use_gravity: ti.i32):
    for i in range(num_particles):
        if inv_mass[i] == 0.0: continue
        if use_gravity != 0:
            vel[i] += gravity * dt
        prev_pos[i] = pos[i]
        pos[i] += vel[i] * dt
        
        pos[i] = clamp_to_box(pos[i])
```

This provides basic collision detection and response, keeping particles within the simulation domain.

### Simulation Results

The complete mesh implementation demonstrates realistic volumetric behavior through geometric constraints, showcasing proper deformation, incompressibility, and complex interactions with boundaries.

<figure>
    <center>
        <img src="dragon.gif">
    </center>
        <figcaption><b>{{fig}}{fig:mesh:dragon}</b> Volumetric mesh simulation of a dragon model, demonstrating edge and volume constraints and unconditional stability.</figcaption>
</figure>




