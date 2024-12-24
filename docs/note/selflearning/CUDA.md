# CUDA入门

​	**加速系统**：又称异构系统，由CPU和GPU组成。加速系统会运行CPU程序，这些程序也会转而启动将受益于GPU大规模并行计算的函数

​	```nvidia-smi```命令查询有关此GPU的信息

## 1 为GPU编写应用程序代码

以下是一个```.cu```文件第一个函数将在CPU上运行，第二个将在GPU上运行

```c++
void CPUFunction()
{
  printf("This function is defined to run on the CPU.\n");
}

__global__ void GPUFunction()
{
  printf("This function is defined to run on the GPU.\n");
}

int main()
{
  CPUFunction();

  GPUFunction<<<1, 1>>>();
  cudaDeviceSynchronize();
}
```

- ```__global__```关键字表明以下函数将在GPU上运行并可全局调用
- 在CPU上执行的代码称为主机代码，在GPU上运行的代码称为设备代码
- 使用```__global__```关键字定义的函数需要返回```void```类型

```GPUFunction<<<1, 1>>>() ;```

- 通常，当调用要在 GPU 上运行的函数时，我们将此种函数称为**已启动**的**核函数**。
- 启动核函数时，我们必须提供**执行配置**，即在向核函数传递任何预期参数之前使用 `<<< ... >>>` 语法完成的配置。
- 在宏观层面，程序员可通过执行配置为核函数启动指定**线程层次结构**，从而定义线程组（称为**线程块**）的数量，以及要在每个线程块中执行的**线程**数量。稍后将在本实验深入探讨执行配置，但现在请注意正在使用包含 `1` 线程（第二个配置参数）的 `1` 线程块（第一个执行配置参数）启动核函数。

```cudaDeviceSynchronize();```

- 与许多 C/C++ 代码不同，核函数启动方式为**异步**：CPU 代码将继续执行*而无需等待核函数完成启动*。
- 调用 CUDA 运行时提供的函数 `cudaDeviceSynchronize` 将导致主机 (CPU) 代码暂作等待，直至设备 (GPU) 代码执行完成，才能在 CPU 上恢复执行。

## 2 编译并运行加速后的CUDA代码

- 编译和运行```.cu```程序

  ```!nvcc -arch=sm_70 -o hello-gpu 01-hello/01-hello-gpu.cu -run```

-  配置线程块数量和每个线程块所包含的线程数量

​		```someKernel<<<NUMBER_OF_BLOCKS,NUMBER_OF_THREADS_PER_BLOCK>>```

- 线程和块的索引

  ```threadIdx.x```&```blockIdx.x```

- 分配将要在GPU和CPU上访问的内存

  ```c
  // Accelerated
  
  int N = 2<<20;
  size_t size = N * sizeof(int);
  
  int *a;
  // Note the address of `a` is passed as first argument.
  cudaMallocManaged(&a, size);
  
  // Use `a` on the CPU and/or on any GPU in the accelerated system.
  
  cudaFree(a)
  ```

- 处理块配置与所需线程数不匹配

  ```c
  - // Assume `N` is known
  int N = 100000;
  
  // Assume we have a desire to set `threads_per_block` exactly to `256`
  size_t threads_per_block = 256;
  
  // Ensure there are at least `N` threads in the grid, but only 1 block's worth extra
  size_t number_of_blocks = (N + threads_per_block - 1) / threads_per_block;
  
  some_kernel<<<number_of_blocks, threads_per_block>>>(N);
  
  __global__ some_kernel(int N)
  {
    int idx = threadIdx.x + blockIdx.x * blockDim.x;
  
    if (idx < N) // Check to make sure `idx` maps to some value within `N`
    {
      // Only do work if it does
    }
  }
  ```

  

- 数据集比网络大

  可给出网络中线程块数的特殊变量：```gridDim```

  ```c
  __global void kernel(int *a, int N)
  {
    int indexWithinTheGrid = threadIdx.x + blockIdx.x * blockDim.x;
    int gridStride = gridDim.x * blockDim.x;
  
    for (int i = indexWithinTheGrid; i < N; i += gridStride)
    {
      // do work on a[i];
    }
  }
  ```

- 错误处理

  许多CUDA函数会返回类型为```cudaError_t```的值

```c
	cudaError_t err;
	err = cudaMallocManaged(&a, N)                    // Assume the existence of `a` 		and `N`.

	if (err != cudaSuccess)                           // `cudaSuccess` is provided by 		CUDA.
	{
  	printf("Error: %s\n", cudaGetErrorString(err)); // `cudaGetErrorString` is 			provided by CUDA.	
	}
```

​	启动定义为返回 `void` 的核函数后，将不会返回类型为 `cudaError_t` 的值。为检查启动核函数时是否发生错误（例如，如果启动配置错误），CUDA 提供 `cudaGetLastError` 函数，该函数会返回类型为 `cudaError_t` 的值。	

```cpp
/*
 * This launch should cause an error, but the kernel itself
 * cannot return it.
 */

someKernel<<<1, -1>>>();  // -1 is not a valid number of threads.

cudaError_t err;
err = cudaGetLastError(); // `cudaGetLastError` will return the error from above.
if (err != cudaSuccess)
{
  printf("Error: %s\n", cudaGetErrorString(err));
}
```

​	最后，为捕捉异步错误（例如，在异步核函数执行期间），请务必检查后续同步 CUDA 运行时 API 调用所返回的状态（例如 `cudaDeviceSynchronize`）；如果之前启动的其中一个核函数失败，则将返回错误。

## 3 利用基本的CUDA内存管理技术来优化加速应用程序

### 3.1 流多处理器



## 4 被加速的c/c++应用程序的异步流和可视化分析











