## Linear Modal Analysis Demo

We implemented the linear modal analysis procedure for a 2D cantilever beam in `src/solid-sim-tutorial/25_linear_modal_analysis/linear.py`

###  Visualizing the Mode Shapes

Following the precomputation steps outlined in the methodology, we used the Finite Element Method to assemble the global mass ($\mathbf{M}$) and stiffness ($\mathbf{K}$) matrices. After applying fixed boundary conditions to one end of the beam, we solved the generalized eigenvalue problem ($\mathbf{K}\mathbf{x} = \lambda\mathbf{M}\mathbf{x}$) to find the lowest-frequency mode shapes ($\mathbf{\psi}_i$) and their corresponding frequencies ($\omega_i = \sqrt{\lambda_i}$).

The resulting modes were visualized by animating the beam's deformation according to $\mathbf{\psi}_i\sin(\omega_i t)$. The animation below displays the first six modes for the following parameter values:

```py 
E = 5e7   # Young's modulus
nu = 0.3  # Poisson's ratio
rho = 500 # Density
# Height : length ratio = 1 : 15
```

<br>

<figure>
    <center>
        <img src="img/lec25/modes_1-6.gif">
        <figcaption><b>{{fig}}{fig:lec25:modes_1-6}</b> Visualization of the First Six Computed Vibration Modes. The modes, ordered from lowest frequency (left) to highest (right), display increasingly complex deformation patterns, from simple bending to S-curves and compressional shapes.</figcaption>
    </center>
</figure>

<br>

### Artifacts of Large Deformations

The primary limitation of linear modal analysis is that it produces visible artifacts when undergoing large deformations. To demonstrate this, we designed a second experiment focused exclusively on the first bending mode.

In this experiment, we animate the beam deforming from its rest state to a state of large deflection. Alongside the deforming beam, we plot two key metrics in real-time:
1.  **Centerline Arc Length:** For a real, inextensible object, the length of its centerline should remain constant during pure bending.
2.  **Total Area (2D Volume):** For a nearly incompressible material, the total area should also be preserved.

As the tip displacement increases, the linear model incorrectly predicts that **(1) the beam's centerline stretches significantly**, and **(2) its total area increases significantly.**.

<br>

<figure>
    <center>
        <img src="img/lec25/linear_model_artifacts.gif">
    </center>
    <figcaption><b>{{fig}}{fig:lec25:linear_model_artifacts}</b> Animation Demonstrating Artifacts of the Linear Model. (Left) The beam deforms according to the first linear mode. (Middle) A plot traces the erroneous increase in centerline length as a function of tip displacement. (Right) A plot traces the erroneous decrease in total area.</figcaption>
</figure>

<br>

These artifacts occur because the linear model cannot account for the geometric nonlinearities that arise from large rotations. This failure is the primary motivation for the methods described in the following section, which are designed specifically to overcome these limitations and accurately simulate large, physically plausible motions.
