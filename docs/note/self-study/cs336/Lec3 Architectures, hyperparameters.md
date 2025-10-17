# Architectures, hyperparameters

## 1 Recap of transformer

![](assets/Pasted%20image%2020250922133400.png)

![](assets/Pasted%20image%2020250922140715.png)

## 2 Common architecture variations

### 2.1 Pre-vs-post norm

![](assets/Pasted%20image%2020250922141531.png)

将layer normalization提前，这样不会影响到残差的路径。 

### 2.2 double norm

将layer norm放入残差流中是不好的

在FFN和注意力层前后都加入了归一化层

![](assets/Pasted%20image%2020250922143334.png)

### 2.3 LayerNorm vs RMSNorm

![](assets/Pasted%20image%2020250922143539.png)
使用RMS Norm更快，更少的操作和更少的参数

### 2.4 Dropping bias terms

大部分现代的transformers 没有bias项

$FFN(x) = \sigma(xW_1)W_2$

Reasons: 减少io，提升了稳定性

### 2.5 Activations

![](assets/Pasted%20image%2020250922150348.png)

#### 2.5.1 Gated Activations(GLU)

GLU的核心思想是用一个**逐元素相乘的线性项 (entrywise linear term)** 来增强（augment）标准的 "线性变换 + ReLU" 部分

![](assets/Pasted%20image%2020250922151149.png)

$xV$的每一项都介于01之间，类似于LSTM中的门控单元
![](assets/Pasted%20image%2020250922151354.png)

### 2.6 parallel layers

同时进行MLP和attention层的计算

![](assets/Pasted%20image%2020250922180843.png)

#### 2.6.1 rotary positioin embeddings

注意力函数只依赖于相对位置(i-j)
$$
e_{i,j}=\frac{x_iW^Q(x_jW_K+a_{ij}^K)}{\sqrt{d_z}}
$$

考虑到向量的内积于任意的旋转都无关

![](assets/Pasted%20image%2020250922183755.png)

RoPE的基本思想不是将位置信息作为向量加到词嵌入上，而是通过**旋转**词嵌入向量（特指在注意力机制中的Query和Key向量）来注入位置信息

将一个高维的Query或Key向量，拆分成许多个二维向量 $(x_1, x_2)$ ，为每一对二维向量分配一个**不同的旋转基准频率**，$m$代表该token在序列中的绝对位置 (Position)，$\theta$: 代表一个预设的、固定的旋转“基准频率”或“旋转速度” (Constant)，将原来的二维向量旋转一个角度$m\theta$,则得到带有位置信息的向量，

![](assets/Pasted%20image%2020250922185032.png)
### 2.7 Variations in position embeddings


## 3 Hyperparameters that matter

### 3.1 Feedforward

经验：

两个超参数：$d_{model}$: $x$的维度，$d_{ff}$: MLP输出的隐藏维度
- $d_{ff} = 4d_{model}$
- ![](assets/Pasted%20image%2020250922190125.png)
- ![](assets/Pasted%20image%2020250922190241.png)
