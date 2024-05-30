## Time Integration

Newton's second law lays the foundation for a series of Ordinary Differential Equations (ODEs) expressed in their continuous forms. This is analogous to how we previously used sampled points in space to discretely represent continuous geometries. Now, we take a similar approach but in the realm of time. By sampling points in time, we can effectively represent time derivatives, such as \\(\frac{\mathbf{d} x}{\mathbf{d} t}\\) and \\(\frac{\mathbf{d} v}{\mathbf{d} t}\\). 

> **{{def}}{def:lec1:time_integration}[Time Integration]** 
When discretizing time into fixed small intervals, we denote the time at the \\(n\\)-th step as \\(t^n\\), commonly referred to as a **timestep**. The length of this interval, or **timestep size**, is given by \\(\Delta t = t^{n+1} - t^n\\). The timestep count, \\(n\\), is typically a whole number starting from zero, making \\(t^0=0 s\\) the starting point of a simulation.

The concept of timesteps leads to the introduction of symbols \\(x^n\\), \\(v^n\\), and \\(f^n\\) to represent the positions, velocities, and forces of nodes at the \\(n\\)-th timestep, respectively. The term **timestepping**, or time integration, refers to the process of calculating \\(x^{n+1}, v^{n+1}\\) from \\(x^n, v^n\\) at each incremental timestep \\(n=0,1,2,\dots\\). For a visual demonstration, consider an Armadillo slingshot animation. Each frame in this animation is computed progressively from left to right, as illustrated in the figure below. In this context, timestepping mirrors a cinematic progression, revealing the evolving dynamics of a system in a step-by-step manner.

<figure><center><img src="img/lec1/slingshot.jpg"><figcaption><b>{{fig}}{fig:lec1:slingshot}</b> Armadillo slingshot frame by frame</figcaption></center></figure>

In the context of this book and the simulation scenarios we examine, a crucial assumption must be emphasized: we always possess exact knowledge of the initial values \\(x^0\\) and \\(v^0\\) at the start of our simulation. Furthermore, for each timestep, we either have a method to calculate \\(f^n\\) based on a physical model, or we have its precise value readily available, as with a constant force such as gravity. This assumption is fundamental to our approach, ensuring that simulations are grounded in known initial conditions and forces, thereby allowing for more accurate and reliable outcomes.
