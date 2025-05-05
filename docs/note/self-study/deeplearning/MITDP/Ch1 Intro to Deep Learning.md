# Intro to Deep Learning

## 1 The Perception 感知机

一个 Perception 是一个 neuron,一个神经网络由大量的神经元组成

### 1.1 Forward Propagation
![](assets/Ch1%20Intro%20to%20Deep%20Learning/file-20250206134624675.png)

bias term: 偏置项，使激活函数在水平方向上移动

X: input

W: weight

Activation Functions: 激活函数，非线性，

三个步骤：
- dot product
- bias
- non-linearty

**Quantifying Loss:**
The loss of our network measures the cost incurred from incorrect predictions
以预测值和真实值为输入,教导神经网路犯错时是怎么做的

- 交叉熵, Binary Cross Entropy Loss, can be used with models that output a probability  between 0 and 1.
- ![500](assets/Ch1%20Intro%20to%20Deep%20Learning/6b7f6a08594ec2bcb85ac0fe6504a28.jpg)
- 均方误差损失，Mean Squared Error Loss, can be used with regression models that output continuous real numbers，
- ![500](assets/Ch1%20Intro%20to%20Deep%20Learning/file-20250216205502570.png)

**Common Activation Functions:**

- ReLU： Rectified Linear Unit 整流，

![](assets/Ch1%20Intro%20to%20Deep%20Learning/file-20250206135638356.png)

激活函数的目的是在神经网络中引入非线性，使其具有处理非线性数据的能力 

## 2 Training Neural Networks

### 2.1 Loss Optimization

我们希望能够找到网络权重使损失最小
​
Our Loss is a function of the network weights

**Gradient Descent 梯度下降：**
![500](assets/Ch1%20Intro%20to%20Deep%20Learning/file-20250216210736223.png)
**back propagation反向传播：**
![500](assets/Ch1%20Intro%20to%20Deep%20Learning/file-20250216211404129.png)
从输出向输出反向传播，通过链式法则计算出每一个独立的权重的小变化对损失函数的影响

**$\eta$，学习率**：$W\leftarrow W - \eta \frac{\partial J(W)}{\partial W}$
基本确定朝梯度方向移动多少

如何寻找合适的学习率？
设计自适应学习算法，

#### 2.1.1 Gradient descent algorithms

- Stochastic Gradient Descent （SGD）:随机梯度下降 。每次循环只挑选B个数据进行梯度下降（minibatch).
- ![500](assets/Ch1%20Intro%20to%20Deep%20Learning/file-20250217085540763.png)
#### 2.1.2 Overfitting 过拟合
Too complex, extra parameters, does not generate well
**Regularization:**
为解决适定性问题或过拟合而加入额外信息的过程

- Dropout: 随机选择一些神经元子集并以一定的概率将其剪枝。Forces network to not rely on any I node
- Early Stopping: 将一部分训练集拿出来进行测试。![500](assets/Ch1%20Intro%20to%20Deep%20Learning/file-20250217091109169.png)当测试集的loss增加时，此时发生过拟合了.找到这个点并停止进行正则化