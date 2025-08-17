# Hardware and Software

## 1 Deeplearning Hardware

### 1.1 GPU
- Memory Modules
- Processor
	- Streaming Multiprocessors(SMs) 类似CPU cores
		- FP32 cores
		- Tensor cores: 特殊的硬件，使用混合精度，FP16乘法，FP32加法，用于小矩阵运算
		- ...

矩阵乘法对GPU来说非常合适，因为输出矩阵中的每个元素之间都是没有依赖的，所以只要将每个元素的计算并行就可以

### 1.2 Programming GPUs

- CUDA
- OpenCLd

### 1.3 TPUs

同样用于矩阵乘法，混合精度，bfloat16


## 2 Deep Learning Software

![](assets/Pasted%20image%2020250808141448.png)

two main stream of deep learning  framework:
- pytorch
- tensorflow

深度学习框架的关键点：
- 能够根据新的想法进行快速设计
- 自动计算梯度
- 在GPU/TPU高效**运行**

### 2.1 Pytorch

**three fundamental concepts**:
- tensor
- autograd
- module: 一个神经网络的层，可能存储这状态或者可学习的权重


`torch.nn`: 像根据layers之间的顺序组合layers来设计模型

`torch.optim`: 使用优化器来应对不同的更新规则

定义自己的`nn.Module`

`Dataloader`

Pretrained Models

Dynamic Computation Graph: 每次计算梯度建立一个新的图，用完扔掉

#### 2.1.1 Static Computationa Graphs

`jit`模块

通过静态图，能够在开始运行之前对图进行优化

**Dynamic Graph Applications:** Recurrent Networks, Recursive Networks, Modular Networks, 网络结构依赖输入

## 3 Tensorflow

![](assets/Pasted%20image%2020250808153234.png)


