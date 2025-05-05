# Introduction of Deep Learning

机器学习： 让机器具备找函数的能力

- **Regression:** The function outputs a scalars数值
- **Classification:** Given Options， The functions outputs the current one

**Training  Steps**：
- Function with unknown parameters(base on domain knowledge)猜测函数的参数
- Define Loss from Training Data
    - Loss ：how good a set of values is
    - Label: 正确的数值
- Optimization:找到最好的weight和bias
    - Gradient Desent:首先随机选择一个值，然后计算偏导，学习率乘上偏导是移动的步长；迭代更新参数
    - 可能出现停在local minima的问题
    - <div align = center><img src="assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250416120740526.png" width = 50%></div>


**Model Bias**: 来自模型的限制，例如linear function就有很大的限制


All Piecewise Linear Curves = constant + a set of (分段函数)

Sigmoid Function: $$y=c\frac{1}{1+e^{1(b+wx_1)}}$$
<div align = center><img src="assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250416150011827.png" width=50%></div>

修改参数来调整拟合的效果
<div align = center><img src="assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250416150826785.png" width = 50%></div>

**New Model : More Features:**
$$
y=b+wx_1\rightarrow y=b+\sum_ic_isigmoid(b_i+w_ix_1)
$$

$$
y=b+\sum_jw_jx_j\rightarrow y=b+\sum_ic_i~sigmoid(b_i+\sum_jw_{ij}xj)
$$
<div align = center><img src="assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250416161444015.png" width = 50%></div>

![400](assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250416221336124.png)

$$
\begin{aligned}
&r=Wx+b
\\& a=\sigma(r)
\\&y=b+c^Ta
\end{aligned}
$$
- feature: $x$
- Unknown parameters: $W,b,c^T,b$,这些组成一个长向量$\theta$


**Loss**: is a function of parameters $L(\theta)$

$$\begin{aligned}
&\theta=\begin{bmatrix}
\theta_1\\\theta_2\\\cdots\\\theta_n
\end{bmatrix}\\
&\theta^*= arg~min_{\theta}L
\\
&g(gradient)=\begin{bmatrix}
\frac{\partial L}{\partial \theta_1}|_{\theta=\theta^0}\\
\frac{\partial L}{\partial \theta_2}|_{\theta=\theta^0}\\\cdots
\end{bmatrix}
\\&g=\bigtriangledown L(\theta^0)\\
&\begin{bmatrix}
\theta_1^1\\\theta_2^1\\\cdots
\end{bmatrix}\leftarrow\begin{bmatrix}
\theta_1^0\\\theta_2^0\\\cdots
\end{bmatrix}-\eta g
\end{aligned}$$

将一整个L划分为很多个batch，每次计算loss，更新参数只是用其中一个batch，

1 epoch = see all the batches once

**ReLU:**
$$y=b+\sum_{2i}c_imax(0,b_j+\sum_jw_{ij}xj)$$
**Activation Function:** 例如Sigmoid,RuLU，在机器学习中统称为激活函数

## 1 Pytorch tutorial
### 1.1 Load Data
#### 1.1.1 Load Data
- `torch.utils.data.Dataset`:stores data samples and expected values
- `torch.utils.data.Dataloader`: groups data in batches, enables multiprocessing
```python
dataset = MyDataset(file)
dataloader = Dataloader(dataset, batch_size, shuffle=True )//Training:True,Testing:False

```

#### 1.1.2 Tensor
```python
x = torch.tensor([[1, -1], [-1, 1]])
y = x.sum()#元素求和
y = x.mean()
y = x.transpose(0,1) #转置
y = x.squeeze(0)#移除大小为1的维度
y = x.unsqueeze(1)#扩展维度，设置为1
w = torch.cat([x,y,z],dim=1)#拼接tensor
```

##### 1.1.2.1 Datatype

```python
torch.float
torch.long
torch.FloatTensor
torch.LongTensor
```

##### 1.1.2.2 Device 
```python
x.to('cpu')#default
torch.cuda.is_available()
x.to('cuda')
```

#### 1.1.3 Gradient Calculation
```python
>>> x = torch.tensor([[1., 0.], [-1., 1.]], requires_grad=True) 
>>> z = x.pow(2).sum()  
>>> z.backwa
>>> rd() >>> x.grad
```


### 1.2 Define Neural Network

- Linear Layer(Full-connected Layer)`nn.Linear(in_features,out_features)`
```python
>>> layer = torch.nn.Linear(32, 64) 
>>> layer.weight.shape 
    torch.Size([64, 32]) 
>>> layer.bias.shape 
    torch.Size([64])
```
- Non-Linear Activation Functions:`nn.Sigmoid()`,`nn.ReLU`

### 1.3 Loss Function
```python
criterion = nn.MSELoss()
criterion = nn.CrossEntropyLoss()
loss = criterion(model_output, expected_value)
```

### 1.4 Optimization Function
```python
optimizer = torch.optim.SGD(model.parameters(), lr, momentum = 0)
optimizer.zero_grad()#reset gradients of model parameters.
loss.backward()#backpropagate gradients of prediction loss
optimizer.step() #adjust model parameters
```

### 1.5 Train
![400](assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250417123723242.png)

![400](assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250417123753286.png)
![400](assets/Lecture%201%20Introduction%20of%20Deep%20Learning/file-20250417123812036.png)

