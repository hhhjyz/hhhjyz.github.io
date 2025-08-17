
_[参考博客](https://frankly6.cn/2025/03/27/EECS498-notes/#lecture-14-%E5%8F%AF%E8%A7%86%E5%8C%96%E4%B8%8E%E7%90%86%E8%A7%A3)_
# Visualizing and Understanding

**what are the intermediate features looking for?**

## 1 First Layer: Visualize Filters

在各种结构非常不同的模型中，都出现了很多查找不同方向边缘的filters,不同类型的颜色与翻转

## 2 Last Layer: 全连接层

使用**t-SNE**算法专门来做高维数据到二维或三维的降维并可视化, 它是非线性的并且擅长保留局部结构, 不过对超参数很敏感并且运算开销很大, [这里](https://zhuanlan.zhihu.com/p/446865293)有一篇粗略的 t-SNE 科普, 而[这里](https://cs.stanford.edu/people/karpathy/cnnembed/)是一个 t-SNE 可视化 CNN 的样例

_Visualizing Activations_

我们也可以激活中间的卷积层, 因为激活函数的存在, 可能会有大量的纯黑色图像, 对于一些非零的卷积核, 我们可以将其与图片对齐以猜测其响应了什么特征

![](assets/Pasted%20image%2020250814211244.png)

_Maximally Activating Patches_

在给定神经网络层级中, 能产生最大激活值的图像片段和区域  

例如我们可以对于输入图片输出相应最高的图块, 根据输出的图块猜测网络在寻找什么特征

![](assets/Pasted%20image%2020250814211611.png)

这种方法也可以来估计哪些输入像素对于网络是真正重要的, 我们比较原图和原图**遮蔽(occlusion)** 后的神经元的响应, 通过相应的区别大小就可以估计输入像素的重要性, 对整张图片的每个部分都尝试抹去, 就可以得到一个神经元的感受野

![](assets/Pasted%20image%2020250814211942.png)

_Saliency via backprop_

我们还可以通过反向传播的方式获得输入图像中每个像素的**梯度**, 这告诉我们对于每个像素其对于决定分类分数的重要性

_Intermediate Features via (guided) backprop_

我们也可以用反向传播去寻找中间特征, 这里用的是一种 guided backprop, 对上游传过来的负梯度设置为 0, 不知道为什么这会让图像更好看

![](assets/Pasted%20image%2020250814212804.png)

_Gradient Ascent_

我们不一定要用测试图像来做可视化, 还可以去合成一个最大化神经元输出的图像, 这引入了**梯度上升(gradient ascent)** 的概念  
我们初始化图像为全 0 像素, 通过反向传播来生成图像, 不要忘记加入正则化函数来让图像拥有可解释性

![](assets/Pasted%20image%2020250814213033.png)

更好的正则化函数会让函数更自然, 所选的梯度上升的神经元越高层, 生成的图像就更有语义

**对抗性样本(adversarial examples)** 是在一个分类的图片加入另一个分类最大化输出的噪声生成的, 即使噪声人类完全无法看出来, 但神经网络会改变其预测结果  
[![](https://s2.loli.net/2025/02/24/ZcVIHtKCwPOxgjB.png)](https://s2.loli.net/2025/02/24/ZcVIHtKCwPOxgjB.png)

[这篇文章](https://blog.csdn.net/StarandTiAmo/article/details/126978601)介绍了对抗性攻击及其防御措施

下面介绍一下**特征反转(feature inversion)** 的想法  
和梯度上升类似, 我们通过前向传播得到图像的特征, 并定义损失函数为另一张图片的特征与我们得到的特征的差异, 尝试最小化这个损失会让我们生成一些奇妙图像 [![](https://s2.loli.net/2025/02/24/JjDMWTyKGSXnofq.png)](https://s2.loli.net/2025/02/24/JjDMWTyKGSXnofq.png)

例如, 浅层的图像会与原始图像相似, 越深层学习的特征就越抽象, 越来越多的低级信息丢失(纹理和颜色), 但是保留了图像的整体结构和形状

Google 的 DeepDream 项目尝试放大图像中的特征, 思想是令 , 这样图像会越来越像网络的 "梦境", 网络将其学到的所有特征都叠加在原始图像上  
[![](https://s2.loli.net/2025/02/24/vN21buyWIcEHk73.png)](https://s2.loli.net/2025/02/24/vN21buyWIcEHk73.png)

例如学习动物的神经网络可以从云彩中看出各种动物来

计算机图形学中有**纹理合成(texture synthesis)**  的概念, 使用 kNN 就能在简单材质上做得很好  
若使用上神经网络, 类似特征反转的思想, 我们希望生成与输入图像的局部纹理特征的整体空间匹配的材质  
我们使用**格拉姆矩阵(Gram matrix)**, 思想是, 我们通过网络运行纹理图像, 对所有的特征两两之间做外积并平均化得到格拉姆矩阵, 这个矩阵扔掉了所有空间信息但是告诉了我们哪些特征两两关联  
通过使用梯度上升匹配目标的格拉姆矩阵, 我们期望得到一些相同纹理的图片 整个流程的思想是这样的:  
[![](https://s2.loli.net/2025/02/24/AnzkeVIPSs8M2rw.png)](https://s2.loli.net/2025/02/24/AnzkeVIPSs8M2rw.png)

一些人将纹理合成与特征重建结合起来, 一个提供特征, 一个提供空间结构, 这就弄出来了**神经风格迁移(neural style transfer)**  
[![](https://s2.loli.net/2025/02/24/PnrIYjloqMp4cvk.png)](https://s2.loli.net/2025/02/24/PnrIYjloqMp4cvk.png)

你可以把多个风格图像放一起应用在内容图片上, 现在的风格迁移已经足够快够实时迁移视频了

总结一下: 我们可以用 **kNN, 降维, 最大激活, 遮蔽, 显著图, 类可视化, 噪声, 特征反转**等方法来理解 CNN  
DeepDream 和 Style Transfer 是我们在可视化过程中得到的一些有趣应用


