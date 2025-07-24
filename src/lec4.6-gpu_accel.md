## GPU-Accelerated Simulation

**Author of this section: [Zhaofeng Luo](https://roushelfy.github.io/), Carnegie Mellon University*

We now rewrite the 2D mass-spring simulator to leverage GPU acceleration.
Instead of directly writing [CUDA](https://developer.nvidia.com/cuda-toolkit), we resort to [MUDA](https://github.com/MuGdxy/muda), a lightweight library that provides a simple interface for GPU-accelerated computations.

The architecture of the GPU-accelerated simulator is similar to the Python version. All function and variable names are consistent with the Numpy version. However, the implementation details are different due to the GPU architecture and programming model. Before delving into the details, let's first get a feeling of the speedup that GPU could bring us from the following gif ({{ref: fig:lec4:cpu_vs_gpu}}).

<figure>
    <center>
    <img src="img/lec4/cpu_gif.gif">
    <img src="img/lec4/gpu_gif.gif">
    </center>
    <figcaption><b>{{fig}}{fig:lec4:cpu_vs_gpu}</b> An illustration of simulation speed of the Numpy CPU (left) and the MUDA GPU (right) versions.</figcaption>
</figure>

### Key Considerations for GPU Programming

To maximize resource utilization on the GPU, there are two important aspects to consider:
- **Minimizing Data Transfer.** In most modern architectures, CPU and GPU have separate memory spaces. Transferring data between these spaces can be expensive. Therefore, it is essential to minimize data transfers between CPU and GPU.
- **Exploiting Parallelism.** GPUs excel at parallel computations. However, care must be taken to avoid read-write conflicts that can arise when multiple threads attempt to access the same memory locations simultaneously.

### Minimizing Data Transfer

To reduce data transfer between the CPU and GPU, we store the main energy values and their derivatives on the GPU. Computations are then performed directly on the GPU, and only the necessary position information is transferred back to the CPU for control and rendering. A more efficient implementation could render directly on the GPU, eliminating even this data transfer, but for simplicity and readability, we have not implemented that here.

To make the code more readable, the variables begin with `device_` are stored in the GPU memory, and the variables begin with `host_` are stored in the CPU memory.

{{imp}}{imp:lec4:energy_definition}[Data structure, MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:definition}}
```
As shown in the code above, the energy values and their derivatives, as well as all the necessary parameters are stored in a `DeviceBuffer` object, which is a wrapper of the CUDA device memory implemented by the MUDA library. This allows us to perform computations directly on the GPU without the need for data transfer between the CPU and GPU.

### Newton's Method

The iterations of Newton's method is a serial process and cannot be parallelized. Therefore, we implement this part on the CPU:

{{imp}}{imp:lec4:gpu_time_integrator}[Newton's method, simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:step_forward}}
```
In this function, `step_forward`, the projected Newton method with line search is implemented, performing necessary computations on the GPU while controlling the process on the CPU.
Any variable begin with `device_` here is a `DeviceBuffer` object on the GPU. To print the values in `DeviceBuffer` for debugging purposes, the common practice is to transfer the data back to the CPU, or call the `display_vec` function (which calls `printf` in parallel on the GPU) implemented in `uti.cu`.

The `update_x` function updates the positions of the nodes to all Energy classes and transfers the updated positions back to the CPU for rendering:

{{imp}}{imp:lec4:gpu_update_x}[Update positions, simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:update_x}}
```
As the Energy classes has already updated its positions, the `IP_val` function no loner needs to pass any parameters, avoiding unnecessary data transfer. 
In fact, it only calls the `val` function of all energy classes and then sum the results together:

{{imp}}{imp:lec4:gpu_ip_val}[Computing IP, simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:IP_val}}
``` 
Similarly for the `IP_grad` and `IP_hess` functions:

{{imp}}{imp:lec4:gpu_ip_grad_hess}[Computing IP gradient and Hessian, simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:IP_grad and IP_hess}}
``` 
Notice that they utilize the parallel operations (`add_vector` and `add_triplet`, which are implemented in `uti.cu`) on the GPU to perform the summation for gradients and Hessians.

### Parallel Computations

In our implementation, parallel computation is primarily employed in the computation of energy and its derivatives, as well as vector addition and subtraction. Let's take the MassSpringEnergy computation as an example. 

#### Energy Computation
{{imp}}{imp:lec4:MassSpringEnergyVal}[Computing energy, MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:val}}
```
The `ParallelFor` function distributes the computation across multiple GPU threads. The captured variables in the lambda function allow access to the necessary data structures within each thread.

#### Gradient Computation
{{imp}}{imp:lec4:MassSpringEnergyGrad}[Computing gradients, MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:grad}}
```
The `atomicAdd` function is crucial in the gradient computation to ensure safe concurrent updates to shared data (different edges can update the gradient of the same node), thus preventing race conditions.

#### Hessian Computation
We utilized the Sparse Matrix data structure to store the Hessian matrix. The computation is parallelized across multiple threads, with each thread updating a specific element of the Hessian matrix. The actual size of the Sparse Matrix is calculated at the start of the simulation, allocating just enough memory for non-zero entries. The main consideration here is to calculate the correct indices for each element during simulation:

{{imp}}{imp:lec4:MassSpringEnergyHess}[Computing Hessians, MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:hess}}
```

