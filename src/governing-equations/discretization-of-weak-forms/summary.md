## Summary

In this lecture, we discretized the weak form of momentum conservation in both space and time, arriving at the system of equations for backward Euler time integration introduced in [the first lecture](./lec1-discrete_space_time.md).

**Spatial Discretization:**  
For spatial discretization, a finite number of points are sampled within the domain, and their displacements are used as the degrees of freedom (DOF) of the simulation. With the interpolation function associated with each DOF, the displacement at any point in the domain can be approximated, limiting the solution of the weak form to $d$ $n$-dimensional function spaces formed by mutually orthogonal interpolation functions, where $n$ represents the number of sample points. In this way, the test function $\mathbf{Q}$ can be conveniently assigned to generate $nd$ equations for solving the $nd$ unknowns.

**Temporal Discretization:**  
The discretization of time connects the acceleration $\mathbf{A}$ to the DOF $\mathbf{x}$ via specific time integration rules. By applying mass lumping and assuming zero traction boundary conditions, we can ultimately derive the discrete form. The integration of interpolation functions will be covered in the next chapter.

In the next lecture, we will discuss boundary conditions and frictional contact in the continuous setting.
