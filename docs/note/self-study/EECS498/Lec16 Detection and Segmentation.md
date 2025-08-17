# Detection and Segmentation

## 1 Slow R-CNN Training

在训练结束时，要给每一个region proposal 打上一个positive, negative或者neutral打标签，接着和ground truth boxes进行比较

训练时会忽略掉neutral 的region proposal 

接着将所有region proposals 会被裁剪成固定的大小，传入独立的卷积神经网络，共享权重，预测类别和边界框，

## 2 Faster R-CNN 
Anchor -> Region proposal -> Object Box

<div align = center><img src = "assets/Pasted%20image%2020250817170321.png" width=60%></div>

## 3 Object Detection Methods

![](assets/Pasted%20image%2020250817170826.png)

## 4 CornerNet

无需Anchors进行检测

主要思想是改变边界框的参数化方式, 只用左上角和右下角来表示边界框  

对于我们要预测的每类对象, 我们会预测出一个 "左上角概率热图" 和 "右下角概率热图", 对于每个像素还预测一个嵌入向量来决定哪些左上角和右下角匹配

![](assets/Pasted%20image%2020250817172131.png)

## 5 Semantic Segmentation 语义分割

给图片中的每一个像素点一个类别标签

<div align = center><img src="assets/Pasted%20image%2020250817172449.png" width = 50%></div>

不用区分统一类别的不同实例

### 5.1 Sliding Window

在像素点周围提取一个小照片，进行分类

### 5.2 Fully Convolutional Network

![](assets/Pasted%20image%2020250817172745.png)

全连接卷积网络只有卷积层, 最终输出是通道数为分类数的三维张量

事先确定组别的数量

为了做出更好的决策, 我们希望决策依赖于输入图像中一块较大的区域, 如果用 3* 3  的卷积核需要很多层才能积累到较大的感受野, 这样的计算可能在高精度图像上非常昂贵  
于是我们需要通过**下采样(downsampling)** 和**上采样(upsampling)** 来加速过程
