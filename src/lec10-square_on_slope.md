# Case Study: Square On Slope*

In this section, based on our learnings from [Frictional Contact](lec9-friction.md), we implement frictional contact for a slope within the optimization time integration framework. We start by extending the contact model used for horizontal grounds in the [Square Drop](lec8.3-square_drop.md) case study to accommodate slopes with arbitrary orientations and locations.

Following this extension, we implement friction for the slope, tested by simulating an elastic square dropped onto it. Depending on the friction coefficient $\mu$, the square either stops at various points on the slope or continues to slide.

The excutable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial) under the `4_friction` folder.