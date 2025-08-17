
**input**: single RGB image
**output**: 一组检测到的对象，包裹类别标签和信息（坐标、长宽高等等）

_Detecting a single object_

使用预训练的CNN提取图像特征，对于Class scores 和 box coordinates 分别生成损失函数，然后加权求和

## 1 Detecting Multiple Objects

### 1.1 Sliding Window

在输入图像的不同区域上分别应用CNN来进行图像分类

但是对于每个大小的box即窗口，有很多不同的位置，那么总共有
$$
\sum_{h=1}^H\sum_{w=1}^W(W-w+1)(H-h+1)= \frac{H(H+1)}{2}\frac{W(W+1)}{2}
$$
个可能的boxes

只移动了一点的窗口可能识别的是一个之前识别过的object

### 1.2 Region Proposals

寻找一组数量有限的能够覆盖所有对象的窗口，通常基于启发式的算法

!!! note "R-CNN: Region-Based CNN"

	首先接受input图像，生成候选图像区域，区域大小不同，都将其变形为固定的大小
	
	对于每个候选区域都将独立地经过一个卷积神经网络，然后这个卷积神经网络将输出每个区域对应的分类和bbox transform

	总的候选类型的数量是`object categories + 1`,1 代表是否是背景。接着使用输出的scores来挑选候选区域的子集进行输出（通过对背景或者类别分数设置阈值或者选取 top K proposals）

	存在的问题是候选区域的大小可能与要检测的对象不完全匹配，添加一个Bounding box regression,R-CNN 需要额外学习边界框, 施加某种变化(transform), 希望边界框更好地包含图像, 有四个参数$(t_x,t_y, t_h, t_w)$
	
	![](assets/Pasted%20image%2020250817104416.png)
	


#### 1.2.1 Comparing Boxes

**Intersection over Union(IoU):** 
$$\frac{Area~of~Intersection}{Area~of~Union}$$
- $IoU > 0.5$: decent
- $IoU > 0.7$: pretty good
- $IoU > 0.9$ almost perfect

#### 1.2.2 overlapping boxes

输出一组非常重叠的区域，但是对应一个object

**Non-Max Suppression(NMS):**
- 首先选择得分最高的框
- 计算得分最高的框和同类别其他框之间的IoU
- 如果IoU>threshold，消除那个框
- 接下来选择下一个得分最高的框，计算其和得分低于它的框的IoU，重复上述操作

对于有大量重叠object的图片的处理存在问题

### 1.3 Evaluating Object Detectors

**Mean Average Precision(mAP):**

- 跑一遍对象检测器
- 对每个分类计算精度(AP), 为**准确度-召回率曲线(Precision Recall Curve)(PR curve)** 下的面积
	- 对于每个类别，我们将对所有的检测按照测试分数进行排序
	- ![](assets/Pasted%20image%2020250817115100.png)
	- 从分数最高的开始，如果匹配到一个ground-truth box，h和他的IoU大于0.5,那么将其标记为positive，删除对应的ground-truth box，否则标记为negative
	- 绘制PR曲线
	- 计算AP=area under PR curve ![](assets/Pasted%20image%2020250817120013.png)
- mAP  = average of AP for each category
- COCO mAP: 计算不同的IoU阈值下的mAP，取平均

### 1.4 Fast R-CNN

_R-CNN运行速度很慢！_

Solution: 交换CNN和图像处理，在图像特征图上提取特征和处理图像大小


![](assets/Pasted%20image%2020250817120906.png)

#### 1.4.1 Crop + Resize Features

要以可微分的方式进行裁剪

**RoI Pool :** 对不固定尺寸输入图输出固定尺寸特征图, 本质上就是先将区域先映射到特征图上再划分为需要的输出部分进行 max pooling, 无论其感受野尺寸如何，最后最大池化得到的大小是一样的

![](assets/Pasted%20image%2020250817122007.png)

**RoI Align:**,避免snapping，而是使用双线性插值来计算所有内容

![](assets/Pasted%20image%2020250817130011.png)


### 1.5 Faster R-CNN

主要的时间消耗在启发式的region proposal算法上，所以可以使用CNN来进行替代

**Region Proposal Network(RPN)**: 应用在特征层来提取region proposals, 其余部分与Fast R-CNN一致

先想象 backbone network 已经输出了特征图, 有一个固定大小的**锚框(anchor box)** 在特征图上滑动, RPN 其实是在训练一个二分类问题, 即锚框是否包含一个对象

![](assets/Pasted%20image%2020250817130749.png)

再增加一个box transform参数用于调整anchor  box的大小

 有多种大小的锚框, 有很多超参数要调(锚框数量、比例、大小......)

总的来说要训练四种loss:
- RPN classification： 是否包含object
- RPN regression: 大小调整
- Object classification: 是什么种类或者背景
- Object regression: object box
![](assets/Pasted%20image%2020250817131346.png)

Faster R-CNN通常被称为两阶段object detector：

1. Run once per image: 
	- Backbone network
	- Region Proposal Network
2. Run once per region
	- 特征切分
	- 预测类别
	- 预测框大小

### 1.6 Single-Stage Object Detection

如果想要继续优化 Faster R-CNN, 我们需要考虑能不能将定位和分类两个部分放到一起, 这叫做单阶段目标检测, 代表算法有 SSD 和 YOLO, 我们直接输出所有类别和边界框变化的方法叫做类别特定回归, 让模拟对每个类别专门回归会使得表现更好

## 2 Summary

两阶段准确率更高，单阶段速度更快

