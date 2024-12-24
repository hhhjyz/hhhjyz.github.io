## 1 随机变量
**Definition**:
设随机试验的样本空间为$S=\{e\}$,若$X=X(e)$为定义在样本空间$S$上的实值单值函数，则称$X=X(e)$为随机变量
***常见的随机变量***：离散型，连续型
## 2 离散型随机变量及其分布
***Definition***
	取值至多可数的随机变量为离散型的随机变量
	![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202409261357421.png)
### 2.1 几个重要的离散型随机变量
#### 2.1.1 0-1分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202409261400041.png)
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202409261406479.png)
对于一个随机试验，如果它的样本空间只包含两个元素，我们总能在$S$上定义一个服从（0-1）分布的随机变量$$X=X(e)=\left\{\begin{matrix}
 0,e=e_1\\1,e=e_2
\end{matrix}\right.$$来描述随机试验的结果，这种随机试验称为Bernoulli试验
#### 2.1.2 二项分布
n重伯努利试验
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202409261927772.png)
#### 2.1.3 泊松分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202409261936794.png)
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202409261936818.png)
#### 2.1.4 超几何分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202409261937055.png)
#### 2.1.5 几何分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410101029944.png)
#### 2.1.6 巴斯卡分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410101030831.png)
## 3 随机变量的分布函数
***定义：*** 随机变量X,若对任意实数x，函数
$F(x)=P(X<x)$称为X的分布函数
**任何随机变量都有相应的分布函数**
***性质***：
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410101037913.png)
## 4 连续型随机变量及其密度函数
***定义：***
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102107425.png)
***性质：***
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102107686.png)
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102110276.png)
### 4.1 几个重要的连续型随机变量分布
#### 4.1.1 均匀分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102134139.png)
#### 4.1.2 指数分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102136339.png)
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102136859.png)
**指数分布的无记忆性**
- 对任何$S>0,P(X-s>t|X>s)=P(X>t)$,在$X>s$的条件下，$X-s$仍然服从参数为$\lambda$的指数分布
- 如果X表示等待时间，那么无记忆性说明只要还没等到，那么剩余等待时间仍然服从参数为$\lambda$的指数分布
- 如果X表示元件寿命，那么无记忆性说明只要还没坏掉，那么剩余寿命仍然服从参数为$\lambda$的指数分布
#### 4.1.3 正态分布
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102142180.png)
- $f(x)关于x=\mu对称$
- $f_{max}=f(\mu)=\frac 1{\sqrt{2\pi}\sigma}$
- $\displaystyle\lim_{|x-\mu|\rightarrow \infty}f(x)=0$
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102202741.png)
![image.png](https://cdn.jsdelivr.net/gh/hhhjyz/photos@main/202410102205593.png)
$$\begin{align}当X\sim N(\mu,\sigma^2)时\\P(X\leq b)=\Phi(\frac{b-\mu}\sigma)\\ 即\frac{X-\mu}\sigma\sim N(0,1)\end{align}$$
## 5 随机变量函数的分布
一般，若已知X的概率分布，Y=g(X),求Y的概率分布的过程为：
- 若Y为离散量，则先写出Y的可能取值：$y_1,y_2\cdots y_j\cdots$，再找出$Y=y_i$的等价时间$X\in D$,得$P(Y=y_i)= P(X\in D)$
- 若Y为连续量，则先写出Y的概率分布函数：$F_y(y)=P(Y\le y)$,找出$(Y\le y)$的等价时间$X\in D$,得$F_y(y)=P(X\in D)$;再求出Y 的概率密度函数$f_y(y)$
>[!note] 注意点 
>关键是找出等价事件

>[!info] attention
>注意常用到复合函数求导：
>$$\frac{\mathrm d(F_x(h(y)))}{\mathrm dy}=f_x(h(y))h'(y)$$

### 5.1 定理2.5.1
设$X\sim f_X(x),g'(x)>0(或g'(x)<0),Y=g(X)$,则Y具有的概率密度函数为$$f_Y(y)=\left\{ \begin{matrix}f_X(h(y))\cdot|h'(y)|,&\alpha<y<\beta\\0&otherwise \end {matrix}\right.$$
这里$(\alpha,\beta)$是Y 的取值范围，h是g 的反函数

若$X\sim N(\mu,\sigma^2),Y=aX+b\rightarrow Y\sim N(a\mu+b,a^2\sigma^2)$


