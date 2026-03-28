
# Thin Elastic Rods
*Author of this lecture: [Žiga Kovačič](https://zzigak.github.io/), Cornell University*.

In our exploration of physical simulation, we have focused on volumetric 3D objects and simplified particle-based methods like mass-spring networks. However, many objects, from a character's hair to the yarn in a sweater, are better described as **rod-like structures**: three-dimensional bodies that are "thin" in two of their dimensions. Simulating these as full 3D solids is computationally wasteful, yet modeling them as simple mass-spring chains is often insufficient.

Why is that? A simple chain of particles connected by springs can resist **stretching**, and can even be augmented to approximate **bending**. But it lacks the underlying structure to properly capture **shearing** (the sliding of cross-sections against each other) and, most critically, **twisting** (torsion). It's the complex interplay of all four of these modes—stretching, shearing, bending, and twisting—that gives real-world rods their characteristic coiling, knotting, and looping behaviors.

### So, how do we build a model that is computationally efficient but physically rich enough to capture all four of these effects?

The key is to track not just the position of a central curve, but also its **orientation** at every point. This augmented representation allows us to measure how the rod bends and twists in space. Two prominent frameworks in computer graphics achieve this: **Discrete Elastic Rods (DER)**, which uses discrete differential geometry, and **Cosserat Rods**, a continuum-based theory. While they start from different theoretical viewpoints, both models ultimately arrive at a similar goal: _equipping a 1D curve with the extra rotational degrees of freedom needed to simulate twisting and bending in a physically principled way_. Here, we will focus on the **Cosserat model**, as it fits elegantly into the optimization-based simulation framework we have been developing.