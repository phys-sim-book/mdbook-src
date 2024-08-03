## GPU Accelerated Simulation
In this section, we extend the 2D mass-spring simulation example to leverage GPU acceleration using CUDA. This process involves porting our Python code to C++ with CUDA to achieve significant performance improvements for large-scale simulations. 
All the implementations can be found at [https://github.com/phys-sim-book/solid-sim-tutorial-gpu](https://github.com/phys-sim-book/solid-sim-tutorial-gpu). This section is in the `simulators/1_mass_spring` folder of the repository.

We utilize the Muda library to facilitate the GPU-accelerated simulation. Muda is a lightweight library that provides a simple interface for GPU-accelerated computations.Take a look at [https://github.com/MuGdxy/muda](https://github.com/MuGdxy/muda).


The architecture of the GPU-accelerated simulator is similar to the Python version. All function and variable names are consistent with the Python version. However, the implementation details are different due to the GPU architecture and programming model.

<figure>
    <center>
    <img src="img/lec4/cpu_gif.gif">
    </center>
    <figcaption><b>{{fig}}{fig:lec4:cpu_gif}</b> Python simulation speed. </figcaption>
</figure>
<figure>
    <center>
    <img src="img/lec4/gpu_gif.gif">
    </center>
    <figcaption><b>{{fig}}{fig:lec4:gpu_gif}</b> Cuda simulation speed. </figcaption>
</figure>

### Key Considerations for GPU Performance
To fully utilize the performance of GPUs, there are two important aspects to consider:
- Minimizing Data Transfer: In most current architectures, the CPU and GPU have separate memory spaces. Transferring data between these spaces can be costly in terms of performance. Therefore, it is essential to minimize data transfers between the CPU and GPU.
- Exploiting Parallelism: GPUs excel at parallel computations. However, care must be taken to avoid read-write conflicts that can arise when multiple threads attempt to access the same memory locations simultaneously.

### Minimizing Data Transfer
To reduce data transfer between the CPU and GPU, we store the main energy values and their derivatives in GPU memory. Computations are performed directly on the GPU, and only the necessary position information is transferred back to the CPU for control and rendering. A more efficient implementation could render directly on the GPU, eliminating even this data transfer, but for simplicity and readability, we have not implemented that here.

To make the code more readable, the variables begin with "device_" are stored in the GPU memory, and the variables begin with "host_" are stored in the CPU memory.

{{imp}}{imp:lec4:energy_definition}[MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:definition}}
```
As shown in the code above, the energy values and their derivatives, as well as all the necessary parameters are stored in a `DeviceBuffer` object, which is a wrapper around the CUDA device memory implemented by the Muda library. This allows us to perform computations directly on the GPU without the need for data transfer between the CPU and GPU.

### Newton's Method Iteration
The primary iteration of Newton's method is a serial process and cannot be parallelized. Therefore, this part of the code is controlled by the CPU.

{{imp}}{imp:lec4:gpu_time_integrator}[simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:step_forward}}
```
In this function, `step_forward` implements the Newton method with line search, performing necessary computations on the GPU while controlling the process from the CPU. Only the positions of the nodes ( `device_x` ) are transferred back to the CPU for rendering. 

Any variable begin with "device_" here is a `DeviceBuffer` objecct, which is only a wrapper around the CUDA device memory. This makes the data inside the object unreadable during the debugging process. The common practice is to transfer the data back to the CPU, or call the `display_vec` function implemented in uti.cu to display any `DeviceBuffer` object (which calls `printf` parallelly on the GPU).


{{imp}}{imp:lec4:gpu_update_x}[simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:update_x}}
```

The `update_x` function updates the positions of the nodes to all Energy classes and transfers the updated positions back to the CPU for rendering. As the Energy classes has already updated its positions, the `IP_val` function no loner needs to pass any parameters, avoiding unnecessary data transfer. Acctually, it only calls the `val` function of all energy classes add the results together. 


{{imp}}{imp:lec4:gpu_ip_val}[simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:IP_val}}
``` 

So is the `IP_grad` function and the `IP_hess` function. 

{{imp}}{imp:lec4:gpu_ip_grad_hess}[simulator.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/simulator.cu:IP_grad and IP_hess}}
``` 


Notice that they utilize the parallel computation on the GPU to do the add operation of gradients and Hessians(`add_vector` and `add_triplet`, which are implemented in uti.cu).

### Parallel Computations
In our implementation, parallelism is primarily employed in the computation of energy and its derivatives, as well as vector addition and subtraction. Let's take the MassSpringEnergy computation as an example. 

#### Energy Computation
The ParallelFor function distributes the computation across multiple GPU threads. The captured variables in the lambda function allow access to the necessary data structures within each thread.

{{imp}}{imp:lec4:MassSpringEnergyVal}[MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:val}}
```

#### Gradient Computation
The `atomicAdd` function is crucial in the gradient computation to ensure safe concurrent updates to shared data (different edges can update the gradient of the same node), thus preventing race conditions.

{{imp}}{imp:lec4:MassSpringEnergyGrad}[MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:grad}}
```
#### Hessian Computation
We utilized the Sparse Matrix data structure to store the Hessian matrix. The computation is parallelized across multiple threads, with each thread updating a specific element of the Hessian matrix. The actual size of the Sparse Matrix is calculated at the start of simulation, allocating just enough memory for non-zero entries. The main consideration here is to calculate the correct indices for each element during simulation.

{{imp}}{imp:lec4:MassSpringEnergyHess}[MassSpringEnergy.cu]
```cpp
{{#include solid-sim-tutorial-gpu/simulators/1_mass_spring/src/MassSpringEnergy.cu:hess}}
```

