# Lecture 4
**Machine learning**
## 1 Supervised Learning 监督式学习
>A task where a computer learns a function that maps inputs to outputs based on a dataset of input-output

根据输入-输出对数据集将输入映射到输出

## 2 Nearest-Neighbor Classification
观察最近K个观测值的类
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410162012134.png)
## 3 Perceptron Learning
***感知器学习***
将数据作为一个整体来查看，并尝试创建一个决策边界
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410162015590.png)
	生成权重向量$(w_0,w_1,w_2)$,获得最佳权重向量是机器学习算法的目标。我们还生成一个输入向量$(1,x_1,x_2)$
	取两个向量 的点积，
	$$h_w(x)=\left \{\begin{matrix}&1,if ~w\cdot x\geq 0\\&0,otherwise\end{matrix}\right.$$
	由于该算法的目标是找到最佳权重向量，因此当算法遇到新数据时，它会更新当前的权重，它使用感知器学习规则来实现这一点：
	$$w_i=w_i+\alpha(y-h_w(x))\times x_i$$
	这种函数的问题在于它无法表示不确定性，因为它只能等于 0 或 1。它采用**硬性阈值**。解决此问题的一种方法是使用 logistic 函数，该函数采用**软阈值**。Logistic 函数可以产生一个介于 0 和 1 之间的实数，这将表示对估计的置信度。该值越接近 1，下雨的可能性就越大。
## 4 Support Vector Machines 支持向量机
除了最近邻回归和线性回归之外，另一种分类方法是支持向量机。这种方法在决策边界附近使用一个额外的向量（支持向量），以便在分离数据时做出最佳决策
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410162108596.png)
最右边的决策边界与每个组保持最大的距离，从而为其内部变化提供了最大的回旋余地，这种类型的边界，尽可能远离它分隔的两个两个类，称为***Maximum Margin Separator***
支持向量机的另一个好处是它们可以表示具有两个以上维度的决策边界，以及非线性的决策边界

![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410162114600.png)
## 5 Regression  回归
回归是函数的监督学习任务，它将输入点映射到连续值，即某个实数。这与分类的不同之处在于，分类问题将输入映射到离散值（Rain 或 No Rain）。

根据输入预测输出的值

## 6 Loss Functions 损失函数
损失函数是量化上述任何决策规则所损失的效用的一种方式。预测越不准确，损失就越大

对于分类问题，我们可以使用0-1损失
$$L(actual,predicted)=\left \{\begin {matrix}&0~if~actual=predicted\\&1~otherwise\end {matrix}\right.$$
预测连续值时，可以使用$L_1$和$L_2$损失函数。在这种情况下，我们感兴趣的时量化每个预测与观测值之间的差异长度。为此，我们将观测值的绝对值或平方减去预测值
$$\begin{align}L1:L(actual,predicted)=|predicted-actual|\\L2:L(actual,predicted)=(actual-predicted)^2
\end{align}$$

## 7 Overfitting 过拟合
过拟合是指模型与训练数据拟合的非常好， 以至于无法推广到其他数据集

## 8 Regularization 正则化
正则化是惩罚更复杂的假设以支持更简单、更一般的假设的过程。我们使用正则化来避免过拟合。
$$cost(h)=loss(h)+\lambda complexity(h)
$$
$\lambda$是一个常数，我们可以用它来调节对成本函数中复杂性的惩罚强度，$\lambda$越高，复杂性的成本就越大

测试我们是否过度拟合模型的一种方法是使用 **Holdout Cross Validation**。在这种技术中，我们将所有数据一分为二：**训练集**和测试**集**。我们在训练集上运行学习算法，然后查看它对测试集中数据的预测能力如何。这里的想法是，通过测试训练中未使用的数据，我们可以衡量学习的泛化程度。

保持交叉验证的缺点是，我们无法使用一半的数据来训练模型，因为它用于评估目的。解决这个问题的一种方法是使用 **_k-Fold_ 交叉验证**。在这个过程中，我们将数据分成 k 个集合。我们运行训练 k 次，每次都省略一个数据集并将其用作测试集。我们最终对模型进行了 k 种不同的评估，我们可以对模型进行平均并估计我们的模型如何泛化而不会丢失任何数据。

## 9 scikit-learn 
==to do ==

## 10 reinforcement learning 强化学习
在每次操作之后，代理都会以奖励或惩罚的形式获得反馈
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410162203513.png)
学习过程从向代理提供状态的环境开始。然后，代理对状态执行操作。基于此操作，环境将向代理者返回一个状态和奖励，其中奖励可以是正的，使行为在未来更有可能，也可以是消极的（即惩罚），使行为在未来的可能性更小。

## 11 Markov Decision Process
==to do==
## 12 Q-Learning
==to do==

## 13 Unsupervised Learning 
在无监督学习中，仅存在输入数据，AI会学习浙西而数据中的模式
### 13.1 Clustering 聚类
聚类是一项无监督学习任务，它获取输入数据并将其组织成组，以便相似的对象最终属于同一组。
### 13.2 k-means Clustering 
k-means 聚类分析是一种用于执行聚类分析任务的算法。它映射一个空间中的所有数据点，然后在空间中随机放置 k 个集群中心（由程序员决定多少个，这是我们在左侧看到的起始状态）。每个集群中心只是空间中的一个点。然后，每个聚类都会分配最接近其中心的所有点，而不是任何其他中心（这是中间的图片）。然后，在迭代过程中，聚类中心移动到所有这些点的中间（右侧的状态），然后再次将点重新分配给现在最靠近它们的聚类。当重复该过程后，每个点都保持在与之前相同的集群中时，我们已经达到了平衡，算法结束了，给我们留下了在集群之间划分的点。

# Lecture 5
## 1 Neural Networks 神经网络
**人工神经网络**是一种受生物神经网络启发的学习数学模型。人工神经网络对数学函数进行建模，这些函数根据网络的结构和参数将输入映射到输出。在人工神经网络中，网络的结构是通过对数据进行训练来塑造的。

在 AI 中实现时，每个神经元的 parallel 是一个连接到其他单元的**单元**。

## 2 Activation Functions
要使用 hypothesis 函数来确定是否下雨，我们需要根据它产生的值创建某种阈值。

一种方法是使用阶跃函数，它在达到特定阈值之前给出 0，在达到阈值之后给出 1。

另一个可能的功能是 Rectified Linear Unit （ReLU），它允许输出为任何正值。如果该值为负数，则 ReLU 会将其设置为 0。

无论我们选择使用哪个函数，我们在上一节课上学到，除了偏差之外，输入还会受到权重的修改，并且这些之和会传递给激活函数。对于简单的神经网络来说，情况也是如此。