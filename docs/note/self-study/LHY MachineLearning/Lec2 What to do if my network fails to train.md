# What to do if my network fails to train
<div align=center><img src="assets/Lec2%20What%20to%20do%20if%20my%20network%20fails%20to%20train/file-20250417153211813.png" width=50%></div>

## 1 FrameWork of ML

**Training**:
- Step1: function with unknown $y=f_{\theta}(x)$
- Step2: define loss from training data
- Step3: Optimization

<div align=center><img src="assets/Lec2%20What%20to%20do%20if%20my%20network%20fails%20to%20train/file-20250417152812060.png" width=50%></div>
## Model Bias

the model is too simple

**Solution**: 重新设计模型使其更有弹性

## 2 Optimization Issue 

没能找到最优的function

可以先从比较浅的Model开始

如果深的model在训练集上表现更差，就是Optimizatioin Issue

**Solution**: More powerful optimization technology

## 3 Overfitting
**Solution**:
- **Data augmentation:** 根据现有的数据添加数据
- 限制模型的弹性：减少参数、神经元，共用一线参数，用比较少的feature
    - Early Stopping
    - Regularization
    - Dropout

<div align=center><img src="assets/Lec2%20What%20to%20do%20if%20my%20network%20fails%20to%20train/file-20250417154756142.png" width=50%></div>

