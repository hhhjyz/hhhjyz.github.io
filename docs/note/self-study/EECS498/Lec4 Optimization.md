# Optimization
$w^* = \arg \min_w L(w)$

**Computing Gradients**: 

- numeric gradient: 加上一个很小的数$\Delta w_i$，计算loss之后求解近似的梯度(_用于检验_ )
- Analytic gradient:  using back propagation（_用于计算_）

## 1 Gradient  Descent

重复地向梯度的反方向前进

```python
w = initialize_weights()
for t in range(num_steps):
	dw = compute_gradient(loss_fn, data, w)
	w -= learning_rate * dw
```

超参数：
- Weight initialize method
- number of steps
- learning rate
#### 1.1.1 Batch Gradient Descent

$$
L(W) = \frac{1}{N} \sum_{i=1}^{N} L_i(x_i, y_i, W) + \lambda R(W)
$$

$$
\nabla_W L(W) = \frac{1}{N} \sum_{i=1}^{N} \nabla_W L_i(x_i, y_i, W) + \lambda \nabla_W R(W)
$$
**stochastic gradient descent(SGD):随机梯度下降**

```python
w = initialize_weights()
for t in range(num_steps):
	minibatch = sample_data(data, batch_size)
	dw = compute_gradient(loss_fn, minibatch, w)
	w -= dw * learning_rate
```

增加的超参数：
- batch size
- data_sampling：打乱数据集

不是计算整个训练集的loss，而是选取一部分较少的数据的loss来计算梯度

**Problems**:

- 标准的 SGD 算法在处理“病态曲率”（ill-conditioned）的损失函数时效率低下。它会因为在陡峭方向上的巨大梯度而来回震荡，同时又因为在平缓方向上的微小梯度而进展缓慢，导致整体收敛速度很慢
- 容易陷入局部最优解或鞍点
- 随机梯度下降中可能受噪声影响严重

**SGD+Momentum**

- SGD： $$x_{t+1} = x_t - \alpha \nabla f(x_t)$$
- SGD+Momentum:$$v_{t+1} = \rho v_t + \nabla f(x_t)$$ $$x_{t+1} = x_t - \alpha v_{t+1}$$
	- typically $\rho=0.9,0.99$

可以想象成有惯性，下一时刻要继承一部分上一时刻的速度,所以不完全按照梯度的方向前进

**Nesterov Momentom**:先按照历史动量**“预判”或“前瞻”一步**，然后在那个**未来位置**计算梯度，再用这个梯度来修正最终的前进方向。

**当小球快要滚到谷底，前方的坡度开始上升时，Nesterov 方法能提前感知到这个变化，并及时“减速”或“调整方向”**。而标准动量法由于只看当前位置，可能会因为惯性太大而冲过头，导致在谷底来回震荡，影响收敛速度。

$$v_{t+1} = \rho v_t + \nabla f(x_t+\rho v_t)$$ $$x_{t+1} = x_t - \alpha v_{t+1}$$
## 2 AdaGrad 自适应学习率

```python
grad_squared = 0
for t in range(num_steps):
    dw = compute_gradient(w)
    grad_squared += dw * dw
    w -= learning_rate * dw / (grad_squared.sqrt() + 1e-7)
```

根据每个维度上梯度的历史平方和，对梯度进行逐元素缩放

由于随着运行时间的累积，梯度的平方和越来越大，除以的数也越来越大，导致学习率的衰减

## 3 RMSProp: "Leak AdaGrad"

```python
grad_squared = 0
for t in range(num_steps):
	dw = compute_gradient(w)
	grad_squared += dw * dw * (1 - decay_rate) + decay_rate * grad_squared
	w -= learning_rate * dw / (grad_squared.sqrt() + 1e-7)
```

为了防止平方和无限增大，相较于AdaGrad 增加了一个衰减率

## 4 Adam(RMSProp + Momentum)

```python
moment1 = 0
moment2 = 0
for t in range(num_steps):
    dw = compute_gradient(w)
    moment1 = beta1 * moment1 + (1 - beta1) * dw //Momentum
    moment2 = beta2 * moment2 + (1 - beta2) * dw * dw //Adagrad or RMSProp
	moment1_unbias = moment1 / (1 - beta1 ** t)
	moment2_unbias = moment2 / (1 - beta2 ** t)
    w -= learning_rate * moment1_unbias / (moment2_unbias.sqrt() + 1e-7)
```

当 t = 0的时候，如果不使用bias的话，假设beta2 = 0.99, 那么`moment2`将会非常小，导致迈出的步子将会非常大，为了修正这个问题，添加一个bias


![](assets/Pasted%20image%2020250726170347.png)


## 5 Summary

### 5.1 First-order Optimization

- 使用梯度进行线性的逼近
- 逐步缩小逼近值

### 5.2 Second-order Optimization


#### 5.2.1 牛顿法 (Second-Order Optimization: Newton's Method)

与只考虑“坡度”的一阶方法（如梯度下降）不同，二阶方法同时考虑了**梯度（一阶导数）**和**海森矩阵（二阶导数）**。海森矩阵描述了损失函数的局部**曲率**，即表面的弯曲程度。 

该方法的核心是使用**二阶泰勒展开**在当前点 w0​ 附近用一个简单的二次函数来近似真实的损失函数 $L(w)$：

$$L(w) \approx L(w_0) + (w-w_0)^T \nabla_w L(w_0) + \frac{1}{2}(w-w_0)^T \mathbf{H}_w L(w_0)(w-w_0)$$
通过直接求解这个二次近似函数的最小值点（令其导数为0），我们得到**牛顿法**的参数更新公式： $$w^* = w_0 - \mathbf{H}_w L(w_0)^{-1} \nabla_w L(w_0)$$ 这一步直接跳向了近似损失曲面的最低点。

##### 5.2.1.1 缺陷

尽管理论上收敛速度快，但牛顿法在深度学习等大规模应用中是**不切实际 (impractical)** 的，主要源于其巨大的计算成本：

- **存储成本**: 海森矩阵 H 的大小是 N x N（N为参数数量）。对于有数千万参数的模型，存储这个矩阵需要海量的内存（复杂度为 `O(N^2)`）。
    
- **计算成本**: 计算海森矩阵的**逆矩阵** H−1 的计算复杂度高达 `O(N^3)`。对于大规模的N，这个计算量是当前硬件无法承受的。
    

#### 5.2.2 拟牛顿法 (Quasi-Newton Methods)

为了克服牛顿法的计算瓶颈，研究者们提出了拟牛顿法。

拟牛顿法的核心思想是，**不直接计算海森矩阵及其逆矩阵**，而是通过若干次迭代来逐步构建一个对逆海森矩阵的**近似**。

##### 5.2.2.1 BFGS 算法

- **BFGS** 是最流行的拟牛顿法之一。
    
- 它通过每一步的梯度信息来更新对逆海森矩阵的近似。
    
- 虽然将单步计算成本从 `O(N^3)` 降至 `O(N^2)`，但仍然需要存储一个 N x N 的矩阵，内存问题依然存在。
    

##### 5.2.2.2 L-BFGS：更实用的改进 (Limited-Memory BFGS)

- **L-BFGS** 是对 BFGS 的关键改进，也是目前最实用的拟牛顿算法。
    
- 它**不存储完整的近似逆海森矩阵**，而是只保存最近 `m` 次迭代的历史信息（如梯度变化量、参数更新量等，`m` 通常是一个较小的数）。
    
- 在需要计算更新方向时，它利用这些有限的历史信息即时地、递归地计算出来。这使得其内存占用大大降低，变得可行。
    

#### 5.2.3 L-BFGS 的适用与局限

尽管 L-BFGS 解决了内存问题，但它在深度学习中依然有严格的使用限制。

- **适用场景**:
    
    - **全批量模式 (full batch)**：当整个数据集可以一次性载入内存进行计算时。
        
    - **确定性环境 (deterministic)**：损失函数没有随机性（例如，关闭了 Dropout、数据增强等）。
        
    - 在这种理想情况下，L-BFGS 通常收敛得非常快，效果很好。
        
- **主要局限**:
    
    - **不适用于小批量模式 (mini-batch)**：在 mini-batch 训练中，梯度是带噪声的、不稳定的。这种随机性会严重干扰 L-BFGS 对曲率的近似，导致其性能急剧下降甚至失效。
        

#### 5.2.4 主流优化器选择总结

考虑到现代深度学习几乎都基于 mini-batch 随机梯度下降：

- **Adam**:
    
    - **首选的默认优化器**。它在各种任务中都表现稳健，收敛速度快，且对超参数不那么敏感。
        
- **SGD+Momentum**:
    
    - 一个强大的备选项。如果愿意投入时间进行**精细的超参数调整**（尤其是学习率调度），它的最终性能**可能超越 Adam**。
        
- **L-BFGS**:
    
    - 仅在特殊情况下考虑使用。即**当你可以承担全批量更新的成本，并且能关闭所有随机性来源时**，可以尝试 L-BFGS 以获得更快的收敛。
        
