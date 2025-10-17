# Lec01:  Overview and Tokenization

部件：
- Tokenizatioin
- model architecture
- training

## 1 Basics

**Tokenization**

- tokens: 由整型数字组成的序列

tokenizers 在字符串和tokens之间进行转换

![](assets/Pasted%20image%2020250903164350.png)

**Architecture**

从transformer开始，
![](assets/Pasted%20image%2020250903164819.png)
但是相较于最初的架构，有了很多比较小的提升

**Training**

- Optimizer
- Learning rate schedule
- batch size
- 正则化
- 超参数

## 2 Systems

### 2.1 Kernels

![](assets/Pasted%20image%2020250903165956.png)

目标：组织计算 通过最小化数据移动来提高GPU的利用率

### 2.2 Parallelism

- data
- tensor
- pipeline
- sequence

### 2.3 Inference

如何实际上使用模型，通过给定的prompt来生成tokens

推理需要的计算由于要用很多次，所以超过了训练需要的计算

两个阶段：
- prefill：模型会并行地、一次性地处理输入序列中的所有词元（Token）。其核心任务是为后续的文本生成建立一个包含上下文信息的“记忆”——即 **键值缓存（KV Cache）**
- decode: 这是一个自回归（Autoregressive）的过程，即模型每次只生成一个 Token，然后将新生成的 Token 添加到输入序列中，再预测下一个 Token，如此循环往复，直到满足停止条件。_这样可能无法充分利用GPU_

**speed up decoding:**
- cheaper model
- speculative decoding

## 3 Scaling Laws

在小规模上做实验，预测大规模的超参数和损失


train tokens = 20 parameters

## 4 Data

通常数据集是许多不同部分的集合，![](assets/Pasted%20image%2020250914151642.png)

**evaluation**：
- Perplexity
- Standardized testing
- Instruction following
- Scaling test- time compute
- LM-as-a-judge
- Full system



## 5 Alignment

使base model变得有用的过程
- 是语言模型遵循指令
- 指定生成的样式
- 安全性

### 5.1 SFT(Supervised Fintuning)

对几个例子在基础模型上做监督学习

### 5.2 Learning form feedback

**preference data:** 对给定的提示生成多个响应


![](assets/Pasted%20image%2020250914152713.png)

**verifiers**


### 5.3 Algorithms

- Proximal Policy Opetimization
- Direct Policy Optimization
- Group Relative Preference Optimization


可以根据用例的大小来选择模型的使用


## 6 Tokenization

将Unicode strings转化为一组整数，我们需要一个过程来encode strings into tokens, 以及一个过程来decode tokens into strings

vocabulary size 是可能的tokens的大小

空格在tonkenizer中也会被考虑，会和下一个单词拼在一起

### 6.1 Work-based tokenization

使用正则表达式将字符串拆分为一系列字符串，接着将每一段分配给一个整数

但是words是很多的，有些words出现次数很少，没有很大必要去学，

### 6.2 BPE(Byte Pair Encoding)

一种用于数据压缩的方法，不再先考虑如何分割，而是在原文本上训练tokenizer

常见的序列用单个的token来表示，少见的序列用更多的token来表示

从将每个byte表示为一个token开始，接着合并最常见的the pair of adjacent tokens 

首先计算每个字节对出现的次数