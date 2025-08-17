
# 卷积神经网络

每个神经元做一个点积运算

the layers of a ConvNet 有按照三个维度排列的神经元：宽度，高度和深度

## 1 Convolutional Layer

Conv layer由一组可学习的fiters组成，卷积神经网络将学习得到滤波器，使其能够在看到某种类型的视觉特征时激活滤波器


**receptive field(感受野)**：神经元连接输入的区域。连接在二维空间中是局部的（沿宽度和高度），但始终沿着输入体积的整个深度充满


### 1.1 Spacial Arrangement

三个超参数控制输出的体积：
- depth: 卷积核的数量，每个卷积核都会在整个窗口内滑动得到一个二维输出，所有的二维输出组合起来得到三维的输出，深度即为卷积核的数量
- stride：滑动的步长。
- zero-padding：使图像的边缘也可以作为一个感受野的中心

输入的边长W，感受野的边长即卷积边长F，步长S，pedding长度P，输出的矩阵边长为$\frac{W-F+2P}{S}+1$，当$S=1$时，设置$P=(F-1)/2$可以确保输入输出的大小相同，上面的输出结果边长计算得到的结果必须是整数，否则超参数的设置会被认为无效

每个深度切片中的所有神经元的权重和bias都是一致的（此处将卷积核在每个位置的滑动看作一个神经元对应一个感受野）

**Locally-Connected Layer**: 放宽参数共享方案

**Dilated convolutions**: 每个神经元对应的感受野内部不是连续的

## 2 Pooling Layer

在连续的卷积层之间插入，功能是逐步减小表示的空间大小，以减少神经网络中的参数和计算量，从而防止过拟合。

池化层对输入的每个深度切片独立运行。

- input size:$W_1 \times H_1 \times D_1$
- hyperparameters: 池化宽度$F$,stride$D$
- 生成的体积：
	- $W_2 = (W_1-F)/S+1$
	- $H_2 = (H_1-F)/S+1$
	- $D_2 = D_1$

常见的两种max池化：$F = 3,S = 2 ~or~ F =2,S =2$

## 3 Normalization

四维张量：$N\times C\times H\times W$

### 3.1 Batch Normalization

在训练的过程中存在协变的问题，即每一层参数的变化都是相互影响的，而每一层的训练又是同时在进行的

- Input:$x:N\times D$
- 训练阶段学习好的缩放和平移参数，在测试阶段固定不变：$\gamma,\beta:D$ 

为**每个通道（Channel）**独立计算均值和方差。这个均值和方差是基于**整个批次**的所有样本在该通道上的所有像素值计算得出的。

**训练阶段：** 

- $\mu_j = \frac 1 N \sum_{i=1}^{N}x_{i,j}$,每个通道的均值，shape is $D$
- $\sigma^2 = \frac{1}{N}\sum_{i=1}^N(x_{i,j}-\mu_j)^2$,每个通道的标准差，shape is $D$
- $\hat {x_{i,j}}= \frac{x_{i,j}-\mu_j}{\sqrt{\sigma_j^2+\varepsilon}}$, Normalized x, 形状和输入相同
- $y_{i,j} = \gamma_j \hat{x_{i,j}}+\beta_j$,输出，形状和输入相同

**测试阶段**



- $\mu_j$,average of values seen during training
- $\sigma^2$,average of values seen during training
- 其余同上

### 3.2 Batch Normalization for ConvNets


![](../../attachments/Pasted%20image%2020250807094450.png)

通常在全连接层或者卷积层之后插入，在非线性层之前

**优点**：
- 使深度的神经网络更容易训练
- 允许使用更高的学习率，加速收敛
- 网络对参数初始化更加鲁棒
- 在训练中起到正则化作用
- 测试时零开销：可以与卷积层融合
**缺点**：
- 理论上尚未被完全理解
- 训练和测试时行为不一致

_不同归一化方法的不同点在于计算均值和标准差的数据范围不同_
## 4 Layer Normalization

为**每个样本（Sample）** 独立计算其所有通道、所有像素的均值和方差。

- **不依赖批次大小**，在任何批次大小下表现稳定。
- **训练和测试行为一致**。

## 5 Instance Normalization

在 `H, W` 维度上进行。它为**每个样本的每个通道**都独立计算均值和方差。可以看作是批次大小为1的特殊BN。

## 6 Group Normalization

这是BN和LN的一种折中。它首先将**通道（C）分成若干组（Group）**，然后在每个组内进行LN，即在 `(C/G), H, W` 维度上进行归一化。

## 7 Components of a Convolutional Network

![](../../attachments/Pasted%20image%2020250807100217.png)

其中卷积层的计算开销最大