# MPI Learning 

- 初始化MPI环境:```MPI_Init(int* argc,char*** argv)```

- 返回 communicator 的大小，也就是 communicator 中可用的进程数量:

  ```MPI_Comm_Size(MPI_Comm communicator, int* size)```

- 返回communicator中当前进程的```rank```:```MPI_Comm_rank(MPI_Comm_communicator, int* rank)```

- 清理```MPI```环境:```MPI_Finalize()```

  ## MPI的发送和接受

​	A进程将需要发送给B进程的所有数据打包好，放到一个缓存中，通信设备将这些数据传送到B进程，B进程确认数据正确之后再接收