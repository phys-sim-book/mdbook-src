# Strain Energy

With the deformation gradient $\mathbf{F}$ serving as a rigorous mathematical measure of local deformation, we can define the elastic potential energy based on $\mathbf{F}$ to more accurately capture the elastic behaviors of solids. $\mathbf{F}$ is measured at every local point within the solid domain. We would measure the elastic potential locally for each point and then integrate these measurements across the entire domain. This approach mirrors the process used in the [2D Mass Spring](lec4-2d_mass_spring.md) case study, where the energy of each spring, weighted by an estimated volume, was summed up in a discrete setting. Here, $\mathbf{F}$ is also known as **strain**, and the elastic potential $P_e$, referred to as **strain energy**, is derived from integrating **strain energy density functions** $\Psi(\mathbf{F}) : \mathbb{R}^{d \times d} \rightarrow \mathbb{R}$ at each material point within the solid domain:
$$
    P_e = \int_{\Omega_0} \Psi(\mathbf{F}) d\mathbf{X}.
$$
In this lecture, we will explore various design choices of $\Psi(\mathbf{F})$ and examine some of their properties.
