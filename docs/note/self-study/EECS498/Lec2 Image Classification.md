
# Image Classification

## 1 Machine Learning: Data-Driven Approach

1. 收集包括图像和标签的数据集
2. 使用机器学习方法训练分类器
3. 使用新的图像来进行评估

**datasets**:
- MNIST： 手写数字识别
- CIFAR10: 多类别的彩色图片
- ImageNet: 1000 类
- MIT Places

### 1.1 Nearest Neighbor

- Train: 记住所有的数据
- Predict： 找到最相似的图片

**Distance Metric**

1. L1 distance: $d_1(I_1,I_2) = \sum_p|I_1^p-I_2^p|$

决策边界容易受到噪声的影响，产生锯齿状的决策边界

#### 1.1.1 Solution:K-Nearest Neighbors

使决策边界更加平滑，减少异常值的影响

**Distance Metric**：
1. L1 distance
2. L2 distance： $d_2(I_1,I_2)= \sqrt{\sum_p(I_1^p-I_2^p)^2}$

超参数的选择：
- K
- Distance Metric

!!! note "设置超参数"

	**GOOD**：将数据集划分为训练集、验证集和测试集，选择在验证集上表现最好的超参数并在测试集上进行评估
	
	**BEST idea**: 交叉验证（cross-validation）
	
	split data into **folds**, 尝试将每个fold作为验证集并最后计算平均的结果
	
	_对于小型的数据集比较有用_



**Universal Validation**

当训练样本数趋向于无穷时，Nearest neighbor function可以表示任何曲线


**Problem: Curse of Dimensionality**

为了完整地覆盖空间，当维度增加时，training points 会指数级地增加

**缺点**：

- 测试时的速度非常慢
- 基于像素的distance metrics参考价值比较低
	- ![](assets/Pasted%20image%2020250713164128.png)
	- 最左侧的图片与右边的三张图片的距离是一样的，但是我们期望shifted的图像相比另外两个应该与原始图片的距离更近

**优点：**

- 使用特征向量作为Nearest Neighbors的输入的效果非常好
