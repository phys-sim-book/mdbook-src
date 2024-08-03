# 2D Frictional Self-Contact*

In this lecture, we implement 2D friction based on our 2D self-contact implementation in [Case Study: 2D Self-Contact](./lec21-2d_self_contact.md). The executable Python project for this section can be found at [https://github.com/phys-sim-book/solid-sim-tutorial](https://github.com/phys-sim-book/solid-sim-tutorial).

For simplicity, we will focus on implementing a semi-implicit version of friction. This means the normal force magnitude $\lambda$ and the tangent operator $T$ will be discretized to the last time step, and we solve the optimization once per time step without further fixed-point iterations that converge to solutions with fully-implicit friction ([Frictional Contact](./lec9-friction.md)) under the `8_self_friction` folder. 
Cuda implementations can be found at [https://github.com/phys-sim-book/solid-sim-tutorial-gpu](https://github.com/phys-sim-book/solid-sim-tutorial-gpu) under the `simulators/8_self_friction` folder.
Combined with the smoothly approximated static-dynamic friction transition in IPC, implementing friction into an optimization time integration framework is as straightforward as adding an extra potential energy.
