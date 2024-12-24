# MPI Learning 

- 初始化MPI环境:```MPI_Init(int* argc,char*** argv)```

- 返回 communicator 的大小，也就是 communicator 中可用的进程数量:

  ```MPI_Comm_Size(MPI_Comm communicator, int* size)```

- 返回communicator中当前进程的```rank```:```MPI_Comm_rank(MPI_Comm_communicator, int* rank)```

- 清理```MPI```环境:```MPI_Finalize()```

  ## MPI的发送和接受

​	A进程将需要发送给B进程的所有数据打包好，放到一个缓存中，通信设备将这些数据传送到B进程，B进程确认数据正确之后再接收

```c
MPI_Send(
    void* data,
    int count,
    MPI_Datatype datatype,
    int destination,
    int tag,
    MPI_Comm communicator)
    
MPI_Recv(
    void* data,
    int count,
    MPI_Datatype datatype,
    int source,
    int tag,
    MPI_Comm communicator,
    MPI_Status* status)
```

## Dynamic Receiving with MPI Probe

```MPI_status```结构体包含三个主要信息：

1. **发送端秩**. 发送端的秩存储在结构体的 `MPI_SOURCE` 元素中。也就是说，如果我们声明一个 `MPI_Status stat` 变量，则可以通过 `stat.MPI_SOURCE` 访问秩。
2. **消息的标签**. 消息的标签可以通过结构体的 `MPI_TAG` 元素访问（类似于 `MPI_SOURCE`）。
3. **消息的长度**. 消息的长度在结构体中没有预定义的元素。相反，我们必须使用 `MPI_Get_count` 找出消息的长度。

```c
MPI_Get_count(
    MPI_Status* status,
    MPI_Datatype datatype,
    int* count)
```

在 `MPI_Get_count` 函数中，使用者需要传递 `MPI_Status` 结构体，消息的 `datatype`（数据类型），并返回 `count`。 变量 `count` 是已接收的 `datatype` 元素的数目。

```MPI_Get_count```函数 用于确定实际的接收liang