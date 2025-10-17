
## 1 Memory Accounting

tensor 是用于存储任何东西的基石，例如parameters, gradients, optimizer state, data, activations

几乎所有东西都用浮点数来表示， 通常数据存储的大小越小，计算起来越快
- float32 ![](assets/Pasted%20image%2020250915151525.png)
- float16, 不适合用来表示非常小或者非常大的数字![](assets/Pasted%20image%2020250915212454.png)
- bfloat16![](assets/Pasted%20image%2020250915215211.png)
- fp8, 通常，前向激活和权重需要更高的精度，因此 E4M3 数据类型最好在前向传递期间使用。然而，在反向传递中，流经网络的梯度通常不太容易受到精度损失的影响，但需要更高的动态范围。因此，最好使用 E5M2 数据格式存储它们![](assets/Pasted%20image%2020250915215525.png)
- 混合精度训练：更高精度意味着更高的精确性，但是需要更多的内存和计算。解决方案是使用bf16,fp8来进行前向传播，fp32来表示参数和梯度。pytorch 提供了AMP库

## 2 Compute Accounting

计算取决于硬件，在torch中需要显式地将数据存储在GPU memory中

pytorch tensors是一些指向已分配内存的指针

![](assets/Pasted%20image%2020250915221805.png)
### 2.1 torch_einops

Einops是一个用于操作命名维度的张量库

```python
x: Float[torch.Tensor, "batch seq heads hidden"] = torch.ones(2, 2, 1, 3)
```

计算矩阵乘法
```python
z = einsum(x, y, "batch seq1 hidden, batch seq2 hidden -> batch seq1 seq2")
```

归约
```python
x: Float[torch.Tensor, "batch seq hidden"] = torch.ones(2, 3, 4)
#old way
y = x.mean(dim=-1)
#new way
y = reduce(x, "... hidden -> ...", "sum")
```

![](assets/Pasted%20image%2020250916141941.png)

Model Utilization Flops(MUF) 