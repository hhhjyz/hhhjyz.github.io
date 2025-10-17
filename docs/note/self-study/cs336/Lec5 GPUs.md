# GPUs

通常计算能力越强，能让大语言模型有更好的表现

![](assets/Pasted%20image%2020250929200158.png)

相比于CPU,GPU有大量的计算单元(ALU)，用于控制的单元则很少

CPU 为了减少延迟（每个线程尽快完成），GPU为了增加吞吐量（整体被处理的数据）

GPU有很多SM(streaming multiprocessors),独立执行blocks,每个SM有很多SP(streaming processor)可以并行执行threads

![](assets/Pasted%20image%2020250929201221.png)

**memory**: L1 和 shared memory在SM中，L2缓存位于芯片上，而global memory则是 GPU 旁边的内存芯片

## 1 Execution model of GPU

三个关键点：
- Threads：并行执行，每个线程根据不同的输入执行一样的指令
- Blocks: 一个线程组，每个block被分配给一个SM到共享内存上
- Warp:线程执行时以warp为单位，通常32个连续编号的线程为一组

![](assets/Pasted%20image%2020250929202033.png)

## 2 Memory model of GPU

![](assets/Pasted%20image%2020250929202116.png)

每个线程可以访问自己的寄存器和block的共享内存， 经过block的信息需要被读/写到全局内存

_Sid thread(TPU):_
![](assets/Pasted%20image%2020250929202426.png)

## 3 Making ML workloads fast on a GPU

**roofline model**: 随着计算密度的增加，一开始受制于内存大小，之后受制于吞吐量
![](assets/Pasted%20image%2020250929210043.png)

_how do we make GPUs go fast:_
- Control divergence: SIMT, 同一个warp的线程无法同时执行不同的命令
- Low precision computation：![](assets/Pasted%20image%2020250929210653.png)![](assets/Pasted%20image%2020250929210930.png)
- Operator fusion：对一组数据连续进行操作，中间不写回存储器
- Recomputatioin：使用更多计算来减少内存访问
- Coalescing memory：DRAM以burst mode 被读取，一个值附近的数据会被一起读出来
- Tiling：对线程进行分组排序来最小化全局内存访问
	- ![](assets/Pasted%20image%2020250929213022.png)
	- 影响tile size的因素：合并内存访问，共享内存大小，划分矩阵维度尽量接近
	- **memory alignment**: 

## 4 Flash Attention

![](assets/Pasted%20image%2020250929214324.png)

online softmax

![](assets/Pasted%20image%2020250929214522.png)
