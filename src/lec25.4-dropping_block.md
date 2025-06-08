## Case Study: Dropping Block*

Now, let’s implement the simple 2D case:

```python
{{#include solid-sim-tutorial/9_reduced_DOF/simulator.py}}
```

Here we use a small stiffness parameter making the body softer to amplify the deformation of the affine body to better demonstrate the difference between ABD and IPC. The blue is the collision mesh, and the red one is the embedding mesh. 

<figure>
    <center>
        <img src="img/lec25/abd_sim.gif">
        <figcaption><b>{{fig}}{fig:lec25:abd_sim}</b> An illustration of ABD simulation.</figcaption>
    </center>
</figure>