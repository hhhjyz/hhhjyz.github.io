# Mixture of Experts

![](assets/Pasted%20image%2020250922191039.png)

混合专家模型有几个称为专家的组件，它们通过动态路由机制稀疏地被激活


## 1 Routing Function

### 1.1 Top-K routing

**token choice top-k**: 每个token根据偏好对专家进行排序

![](assets/Pasted%20image%2020250929133734.png)

### 1.2 Shared Expert

大量的专家会带来更多的参数计算开销

![](assets/Pasted%20image%2020250929142246.png)

使用更细粒度和更多的专家。

引入共享专家，这些专家并非由路由器动态选择，而是**对所有输入都始终激活**

### 1.3 train MoEs

训练时不能开启所有专家，不然计算开销太大了

#### 1.3.1 RL for MoEs

梯度变化和复杂度意味着这种方法无法被广泛地应用




#### 1.3.2 Stochastic approximations
![](assets/Pasted%20image%2020250929143958.png)
为路由器的决策过程引入可学习的随机性

#### 1.3.3 Heuristic balancing losses

如何摆脱局部最优？

如果一个专家被频繁使用（即 $f_i$​ 很大），那么辅助损失对它的梯度会更大，这会“惩罚”路由器对这个专家的选择，**鼓励它去选择其他不常用的专家**。这种动态的“降权”机制有效地平衡了不同专家之间的利用率，确保了 MoE 模型的**系统效率和训练稳定性**

#### 1.3.4 per-expert biases

_deepseek V3_

softmax分数加上一个偏置项，学习一个简单的在线算法，如果专家得到的token太少了，那么就加上学习率乘一个梯度，让bias变大

## 2 Upcycling





