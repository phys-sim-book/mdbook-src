# 3D Frictional Self-Contact

In 3D, the contact between the solid domain boundaries represented as triangle meshes can be reduced to point-triangle and edge-edge contacts. Intuitively, the point-edge contact pairs in 2D extend directly to 3D as point-triangle pairs. However, even if we prevent all point-triangle interpenetrations in 3D, the triangle meshes can still penetrate each other. This necessitates accounting for edge-edge pairs, especially when the resolution of the mesh is not very high.
