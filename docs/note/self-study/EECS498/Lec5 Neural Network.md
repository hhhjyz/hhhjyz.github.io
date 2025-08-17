
- Problem: Linear classifier is not powerful
- Solution:
	- Feature transfers

**Image Features**
- Color Histogram
- Histogram of Oriented Gradients (HoG)方向梯度直方图：
	1.计算每个像素的边缘方向强度。
	2.将图像划分为 nxn 的区域。
	3.在每个区域内，计算一个由边缘强度加权的边缘方向直方图。
- Bags of words(_Data Driven!_)
	- Build codebook,从一系列图像中 **提取随机图像块 (Extract random patches)**，然后 **将这些图像块进行聚类，以形成由“视觉词汇 (visual words)”组成的“码本 (codebook)”**
	- 对图片使用codebook中的图像块进行编码
- 可以将不同的特征向量拼接起来，组合成一个长向量

## Neural Networks

-  Linear Score Function: $f=Wx$
-  2-layer Neural Networks: $f = W_2 \max{(0, W_1 x)}$

### Fully-Connected Neural Networks

![](assets/Pasted%20image%2020250729101517.png)

### Deep Neural Networks 

将神经网络推广至多层
0,x
**depth**: number of layers
**layers**: 权重矩阵的数量
**width:** size of each layer 向量长度

## Activation Functions

如果不使用激活函数的话，权重矩阵相乘仍然相当于一个linear classifier

- $ReLU(x) = \max(0,x)$: Rectified Linear Unit
- $\sigma(x) = \frac{1}{1+e^{-x}}$：Sigmoid
- $\tanh(x)$
- $Leakly ReLU(x)=\max(0.1x, x)$
- $Maxout(x): \max(w_1^T x + b_1, w_2^T x + b_2)$
- $ELU(x) = \left\{\begin{matrix}x ,x > 0 \\ \alpha(e^x - 1) x \leq 0 \end{matrix}\right.$

ReLU is a good default choice for most problems

**Neurons**: organized into regular layers for computational efficiency

## Space Warping

data points 都在高维空间中，线性分类器的每一行在输入空间的特征空间中产生了一些平面
![](assets/Pasted%20image%2020250729111317.png)
![](assets/Pasted%20image%2020250729113133.png)

隐藏层增加->越来越复杂的决策边界

## Regularization

不通过**减小模型本身的规模**来防止过拟合，而是**先构建一个足够大的、有能力过拟合的模型（高容量模型），然后使用L2正则化等技术来约束它的复杂度**。

## Universal Approximation

一个**足够大**的、**仅包含一个隐藏层**的神经网络，原则上可以**模拟（或逼近）任何复杂的连续函数**，并且精度可以达到我们想要的任何程度

使用多个ReLU function 组合可以得到任意*Bump Function*,从而可以用大量的的 Bump Function 来拟合连续函数
## Convex Functions 凸函数


A function $f : X \subseteq \mathbb{R}^N \rightarrow \mathbb{R}$ is **convex** if for all $x_1, x_2 \in X, t \in [0, 1]$,
$$f(tx_1 + (1-t)x_2) \le t f(x_1) + (1-t) f(x_2)$$

Generally speaking, convex functions are easy to optimize: can derive theoretical guarantees about converging to global minimum*

- **一般来说，凸函数易于优化：** 可以从理论上保证其收敛到**全局最小值***。
#### **大多数神经网络需要进行非凸优化**

- 关于收敛性的保证很少或没有。
- 根据经验，它似乎仍然有效。
- 这是一个活跃的研究领域。