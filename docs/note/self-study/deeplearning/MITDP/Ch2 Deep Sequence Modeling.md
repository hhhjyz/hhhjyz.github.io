# Deep Sequence Modeling 

根据过去的信息进行预测

**Sequence Modeling Applications:**
- Many to one : Sentiment Classification,根据一句话判断情绪是消极的还是积极的
- One to Many: 根据一张图片来输出一个标题
- Many to Many: 机器翻译

## 1 Neurons with Recurrence


![](assets/Ch2%20Deep%20Sequence%20Modeling/file-20250220183912200.png)

![](assets/Ch2%20Deep%20Sequence%20Modeling/file-20250223215810807.png)
将H视为记忆项，由神经元和网络共同维护，表示了各个独立的网络之间的关系

$$\hat{y_t}=f(w_t,h_{t-1})$$

## 2 Recurrent Neural Networks(RNNs)

关键思想： RNN在维护状态，每次序列被处理时更新状态