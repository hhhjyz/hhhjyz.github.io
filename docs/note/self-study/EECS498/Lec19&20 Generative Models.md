# Generative Models

## 1 监督学习vs无监督学习

监督学习需要数据集(需要人类注释), 包含数据(x)和期望输出(y), 监督学习的目标就是学习一个 $f: x\rightarrow y$

无监督学习只有数据(x), 无需人类注释, 目标是学习数据的某种隐藏结构, 这里的任务有聚类、降维(PCA / t-SNE)、特征分析([自编码器](https://zhuanlan.zhihu.com/p/133207206))、[密度估计](https://zhuanlan.zhihu.com/p/150067666)等

## 2 Discriminative vs Generative Models

区别在于基础概率模型不同ren

- data: x
- label: y

此处p指概率密度函数

- **判别模型(discriminative models)**：学习概率分布$p(y|x)$, 根据输入数据预测标签，通常与监督学习一起出现
- **生成模型(Generative models):** 学习概率分布$p(x)$
- **Conditional Generative Models:** 学习概率分布$p(x|y)$

!!! question "如何理解三种模型的不同？"
	在判别模型中, 我们想要学习 $p(y|x)$, 然而生成模型想要学习 $p(x)$, 其中条件生成模型想要学习 $p(x|y)$  
	
	以分类任务为例, 因为 p(x) 是归一化的($\int p(x)\mathrm dx = 1$), 这会带来某种图像的"竞争", 在 $p(y|x)$ 中, 存在的是标签(y)的竞争而不是图像(x)的竞争, 判别模型必须为图像选择一个分类, 即使图像本身不在分类中, 这可以说是对抗性攻击的一个基础。当我们提供不合理的输入时，判别模型没有能力告诉我们出错了
	而生成式模型需要有对视觉世界有深刻理解, 从而拒绝不合理的图片为一个现实中的分类, 比如说现实中不存在两条腿的狗。他学习的是所有照片可能出现的概率
	
	根据贝叶斯公式$P(x|y) = \frac{P(y|x)}{P(y)}P(x)$ ​, 这些模型之间不是独立的, 说明条件生成模型可以由判别模型和生成模型构造, 关键还是如何架构生成式模型
	![](assets/Pasted%20image%2020250819115007.png)

判别模型可以给数据加标签，并学习特征

生成式模型可以检测离群点，不使用标签来学习特征，并通过采样来生成与输入数据匹配的新数据

那么Conditional Generative Model 不仅可以添加标签，也可以检测离群点，根据输入的标签来生成新的数据


## 3 Generative Models
生成式模型的主要分类为显式密度函数(能计算 p(x))和隐式密度函数(不能计算 p(x), 但能采样得到 p(x))

![](assets/Pasted%20image%2020250819134224.png)

### 3.1 Autoregressive models自回归模型

具有显式密度函数

目标： 写下一个显式密度函数：$p(x) = f(x,W)$

对于显式密度函数, 我们希望数据集的可能性(likelihood)被最大化

![](assets/Pasted%20image%2020250819134741.png)

假设$x = (x_1,x_2,...,x_T)$,x由很多的子部分组成

接着应用链式法则来分解概率密度$p(x) = p(x_1)p(x_2|x_1)(p(x_3|x_1,x_2))... = \Pi_{t=1}^T  p(x_t|x_1,...,x_{t-1})$，通过这个公式，我们可以想到RNN

#### 3.1.1 Pixel RNN

![](assets/Pasted%20image%2020250819140030.png)

从左上角开始, 我们对每个像素用 LSTM 算 RGB, 然后往右边和下方扩展, 每个像素是显式依赖于左边和上方的像素, 并隐式依赖于左上方所有的像素的

但是它在训练和测试中都很慢，$N\times N$ image requires $2N-1$ sequential steps

#### 3.1.2 PixelCNN

仍然从左上角开始生成图像，用掩码卷积来依赖以来关系

从左上开始, 我们用一个卷积核想要在有限的感受野内预测当前像素, 然而因为当前右下的像素还未生成, 卷积核的右下部分要 mask 一下

然而因为自回归模型要对像素一一采样, 生成速度还是较慢

#### 3.1.3 Summary

优势是具有显式密度函数，容易评估，缺点是生成速度比较慢

### 3.2 Variational Autoencoders 自动编码器

在 VAE 中我们不能计算概率密度函数的精确值, 但可以求出它的一些下界, 所以我们的目标转变为最大化概率密度函数的下界
#### 3.2.1 Regular, Non-Variational Autoencoders

不是一个概率模型，而是一个常规模型，普通的自编码器是一种无监督学习方法, 旨在学习图像的特征表示

![](assets/Pasted%20image%2020250819141432.png)

在有监督学习中，我们有明确的标签（例如，图片和它对应的“猫”或“狗”的标签）。但是在这里，对于一张输入的图片，我们并没有一个“标准答案”来告诉我们它对应的“正确”特征向量应该是什么样的。特征是潜在的、抽象的，需要模型自己去发现

我们希望通过输入 $x$ 训练一些特征 $z$，如果能从 $z$ 中重建出输入 $\hat{x}$，说明特征 $z$ 是有效的。

从 $x \to z$ 的过程叫做 encode，从 $z \to \hat{x}$ 的过程叫做 decode，我们要训练两个神经网络 encoder 和 decoder，结合起来就叫做自编码器。这里的损失函数为 $\|x - \hat{x}\|_2^2$，这里的 $z$ 要相对于 $x$ 而言非常低维，否则就不能叫做特征了。

在训练结束后, 我们就不需要 decoder 了, 而是使用 encoder 来完成其他下游任务, 它是无监督的学习模型, 不是概率性的, 所以无法生成新图像; 另一方面来说, 它的表现也堪忧

#### 3.2.2 VAE

- 从原始数据中提取特征z
- 从模型采样来生成新数据

所以 VAE 引入了概率，我们希望学习特征 $z$，并且能够从训练好的模型中采样生成新数据。

![](assets/Pasted%20image%2020250819143544.png)

在训练结束后，我们对特征 $z$ 赋予一个先验分布概率，然后 decoder 会输出图像的概率分布，这里的输出概率是通过输出每个像素的平均值 $\mu_{x|z}$ 和协方差矩阵对角线 $\Sigma_{x|z}$ 完成的(假设潜在分布满足正态分布, 不输出整个矩阵因为过大了)。

![](assets/Pasted%20image%2020250819143819.png)

如何训练这个模型？我们还是要最大化 $\sum \log p(x^{(i)})$。如果对于 $x$ 我们能观察 $z$, 我们可以训练条件生成模型 $p(x|z)$, 但我们观察不到 $z$,

一个尝试是边缘化(marginalise)未观察的 $z$, $p_\theta(x) = \int p_\theta(x, z)dz = \int p_\theta(x|z)p_\theta(z)dz$

但是我们无法积分，所以回到贝叶斯公式, $p_\theta(z|x) = \frac{p_\theta(x|z)p_\theta(z)}{p_\theta(x)}$

这里的问题是没法算 $p_\theta(z|x)$ , 所以我们尝试训练另一个网络(encoder)学习 $q_\phi(z|x) \approx p_\theta(z|x)$

![](assets/Pasted%20image%2020250819143450.png)

KL 散度是两个分布之间差异的度量，推导后 $\log p_\theta(x)$ 函数可以表示为

$$\log p_\theta(x) = \mathbb{E}_{z \sim q_\phi(z|x)}[\log p_\theta(x|z)] - D_{KL}(q_\phi(z|x), p_\theta(z)) + D_{KL}(q_\phi(z|x), p_\theta(z|x))$$

$$\ge \mathbb{E}_{z \sim q_\phi(z|x)}[\log p_\theta(x|z)] - D_{KL}(q_\phi(z|x), p_\theta(z))$$

最后一项我们没法算，所以我们通过使得 $q_\phi(z|x) \approx p_\theta(z|x)$ 把这项直接扔掉了，这就是我们不能计算概率密度函数的精确值的原因，这是一种经典的概率论技巧。

这样我们计算出了$\log_{p_\theta}(x)$的下界


!!! example "Fully-Connected VAE"
	![](assets/Pasted%20image%2020250819145637.png)
	此处的768应该是28* 28=784

**训练**： 最大化下界

![](assets/Pasted%20image%2020250819151426.png)

训练 VAE 的流程是这样的, 我们想要最大化 $E_{z \sim q_{\phi}(z|x)}[\log p_{\theta}(x|z)] - D_{KL}(q_{\phi}(z|x), p_{\theta}(z))$

先取一个 batch 的输入 x, 跑 encoder 网络后会得到符合隐变量 z 的分布, 用这个分布来算 $D_{KL}(q_{\phi}(z|x), p_{\theta}(z))$
我们选择的这些分布都是某种高斯分布, 所以 DL 散度的计算可以展开, 
然后从预测分布中采样获得 z, 喂给 decoder, 希望采样获得 $\hat{x}$(数据重建), 并计算 $E_{z \sim q_{\phi}(z|x)}[\log p_{\theta}(x|z)]$

图中蓝色和绿色的部分相互矛盾, 蓝色希望更接近原始数据, 而绿色部分希望限制 z 满足某种高斯分布
之后我们就能生成新数据了

![](assets/Pasted%20image%2020250819152041.png)

z 代表某种高维信息, 通过调整 z, 我们可以做到调整图像的不同特征, 例如人的表情, 物体的朝向, [VAE 论文](https://arxiv.org/pdf/1312.6114)中有这样的可视化

VAE 的优点是快速, 有丰富的隐变量 z, 缺点是生成的图像有点模糊, 最大化的是下界而非 p(x), 我们不禁想它能不能与自回归模型结合一下, [VQ-VAE2](https://arxiv.org/pdf/1906.00446) 模型做到了平衡, 大致思想是 VAE(离散映射 z) + PixelCNN(在隐变量空间 z 跑), 效果特别好

### 3.3 GANs(Generative Adversarial Networks



