# MPI for Distributed Memory System

<div align = center><img src="assets/Pasted%20image%2020250712115202.png" width = 50%></div>

使用**消息传递**对分布式内存系统进行编程

**进程**：运行在一个“核-内存对”上的程序

`mpiexec -n <number of processes> <executable>`

## MPI 程序

### `MPI_Init`和`MPI_Finalize`

```cpp
int MPI_Init(
    int* argc,
    char*** argv
)
```
返回错误码

```cpp
int MPI_Finalize(void)
```

### 通信子

**通信子**指的是一组可以互相发送消息的进程集合

`MPI_COMM_WORLD`是由所有进程组成的通信子

!!! note 获取`MPI_COMM_WORLD`的信息
    ```cpp
    int MPI_Comm_Size(
        MPI_Comm comm,/*in*/
        int* comm_sz_p/*out*/
    );
    ```
