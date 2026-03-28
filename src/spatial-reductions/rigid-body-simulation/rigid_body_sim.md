# Rigid Body Simulation*

**Author of this lecture: [Wenxin Du](https://dwxrycb123.github.io/), University of California, Los Angeles*

To extend the IPC method to rigid body contact, we can employ subspace methods to effectively reduce the system's degrees of freedom (DOFs). Since a rigid body serves as an idealized model for extremely stiff real-world objects with negligible deformation, it does not require a large number of DOFs. This observation motivates the Affine Body Dynamics (ABD) {{#cite lan2022affine}} method. Depending on the choice of subspace, this approach can also be adapted into various algorithms suitable for different categories of soft bodies.