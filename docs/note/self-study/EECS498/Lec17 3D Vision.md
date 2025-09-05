# 3D Vision

两个关键问题：
- 从单张图片预测3D形状
- 处理3D输入数据

今天的讨论基于完全监督的假设

<div align = center><img src = "../assets/Pasted%20image%2020250818104641.png" width = 50%></div>

## 1 **3D 形状表示**

<div align = center><img src= "../assets/Pasted%20image%2020250818104800.png" width = 70%></div>

### 1.1 Depth Map

Depth Map的作用是告诉我们图像中的每个像素距离相机的距离


<div align = center><img src="../

assets/Pasted%20image%2020250818105009.png" width = 50%></div>

RGB image + Depth image = RGB-D Image(2.5D,因为局限是无法捕捉被遮挡物体的结构)

#### 1.1.1 Predicting Depth Maps

使用Fully Convolutional Network, 类似语义分割，但是实际上不太可能实现

**Scale / Depth Ambiguity:** 在看一张图片的时候，你无法真正分辨物体的大小，因为有远有近

![](assets/Pasted%20image%2020250818110251.png)

所以在单个2D凸显中绝对尺寸和绝对深度是混淆的

这个问题可以通过改变损失函数来解决, 如果网络预测的深度为整个深度的一个固定比例, 我们认为这是 0 loss 的

![](assets/Pasted%20image%2020250818111240.png)

### 1.2 Surface Normals

想法上和深度图很相似, 只不过我们给每个像素分配一个垂直于该表面的单位向量(法线), 也可以弄一个 FCN 来训练, 损失函数为法向量间的角度差异  

Per-Pixel Loss: $\frac{x\cdot y}{|x||y|}$

缺点还是不能表示被遮挡的部分

### 1.3 Voxels

!!! note
	**体素(Voxel)** 是 **体积(Volume)** 和 **像素(Pixel)** 的结合词, 相当于三维空间的像素

它把三维空间分为了一系列的小立方体(体素), 每个体素都能存储信息(颜色、密度、表面法线......)

这种表示对于高分辨率需要大量的存储空间(1024^3 的体素网格需要约 4GB), 处理速度会很慢

#### 1.3.1 Processing Voxel Inputs: 3D Convolution

![](assets/Pasted%20image%2020250818112759.png)

和二维的CNN类似

#### 1.3.2 Generating Voxel Shapes

![](assets/Pasted%20image%2020250818113403.png)

使用per voxel 交叉熵损失函数

3D 卷积非常昂贵, 有时人们用**体素管(voxel tubes)** 的思想只使用 2D CNN 来完成这个任务,将输出的通道维度解释为深度

但是z方向的平移不变性失去了

#### 1.3.3 Scaling Voxels: Oct-Trees

为了解决体素网格占用空间过大的问题, 我们可以引入多重分辨率的体素, 使用低分辨率体素去构建主要结构, 高分辨率体素去构建细节

![](assets/Pasted%20image%2020250818121841.png)

#### 1.3.4 Scaling Voxels: Nested Shape Layer

使用正负空间的组合来预测形状，我们把整个形状用多个稀疏的体素层相加减来表示

### 1.4 Implicit Functions

学习一个函数来分辨任意的点是在形状内还是形状外，输出某个点被对象占据的概率

3D对象的表面：$x: o(x) = \frac 12$

**Same idea**: signed distance function（SDF） 给出了点到图形表面的欧几里得距离，正负表示在里面/外面

### 1.5 Pointcloud

用3D空间中的一组点来表示形状，比voxel grid 更具有适应性，可以改变空间中不同点的点云密度

#### 1.5.1 Processing Pointcloud Inputs: PointNet

![](assets/Pasted%20image%2020250818123310.png)

我们不关心图中点的顺序

#### 1.5.2 Generating Pointcloud Outputs

![](assets/Pasted%20image%2020250818124214.png)

**Loss Function:**

- Chamfer distance 倒角距离：![](assets/Pasted%20image%2020250818124755.png)
	- ![](assets/Pasted%20image%2020250818124826.png)
	- _第一项_：对每个蓝色点，找到其最近的邻近橙色点，计算他们之间的欧几里得距离，然后相加
	- _第二项：_ 对每个橙色点，找到其最近的邻近蓝色点，计算他们之间的距离，相加

### 1.6 Triangle Mesh

用一组三角形来表示形状，三维物体的表面被划分为许多小的三角形片, 它有适应性, 支持几何运算, 复杂平面表示，显式表示了图形

- **Vertices**: 3D空间中的一组V points
- **Faces：** 由点构成的一组三角形

#### 1.6.1 Predicting Meshes: Pixel2Mesh

![](assets/Pasted%20image%2020250818132952.png)

**Key Ideas:**
- Iterative Mesh Refinement 迭代网格细化：我们不从头创建网格对象, 而是有一些初始模板(例如上图的椭球), 然后通过不断的更新输出匹配的三角网格
- Graph Convolution: 层的输入是一个图以及附加到图各个顶点的特征向量, 输出是各个顶点新的特征向量, 对于一个顶点, 其特征向量应该取决于其局部感受野的输入特征向量![](assets/Pasted%20image%2020250818133444.png)
- Vertex-Aligned Features: 对于网格中的每个顶点，想要从表示的图像中获取特征向量，表示图像在那个点的视觉外观。我们希望将图像信息混合到图卷积神经网络(GCN)中, 这里我们将三角网格投影到图像上, 对于每个投影点用双线性插值, 使得顶点的特征向量与图像平面中特征的位置对齐, 类似于 RoI-Align
- Loss Function: 一种形状有多种表示, 我们希望损失函数不受不同表示方式的影响, 所以我们将 GT 采样为点云(离线), 同时将预测网格采用为点云(在线), 然后跑 chamber distance![](assets/Pasted%20image%2020250818134603.png)
	- 在二维中我们用 IoU 来比较形状, 到三维时我们可以用上文的那种方式, 算 chamber distance 来比较 3D 图形 .问题是 chamber distance 对离群值十分敏感, 一般我们用 F1 score (准确率和召回率的调和平均数)来比较 3D 形状
	- 我们想象将每个预测点扩大为一个球体, 若 GT 点落在内部则认为预测正确 。如果更关心精确率或召回率可能需要别的指标

在 3D 形状中, 我们需要选取坐标系, 一般可以用**规范坐标系(canonical coordinates)**, 对每类物体规定一个标准方向, 例如椅子的前方就代表 Z 轴正方向 。另一种选择是**视图坐标系(view coordinates)**, 以相机为原点, 更容易实现 。规范坐标系倾向于过拟合, 而视图坐标系泛化更好


Mesh R-CNN 是 R-CNN 的三维版本, 给出 RGB 图像中每个对象的 3D 形状  
我们希望得到一个三角网格, 但是初始模板会限制一些变形(拓扑上不一致), 所以我们可以先生成一个粗糙的体素预测, 然后将其变为三角网格, 再进行精细化迭代  
[![](https://s2.loli.net/2025/03/18/GrORL2qZBxM8iwF.png)](https://s2.loli.net/2025/03/18/GrORL2qZBxM8iwF.png)

最后需要一些正则化来帮助可视化, 如果结果不尽如人意, 可能是 2D 对象检测的问题, 例如在遮挡的部分猜错了

