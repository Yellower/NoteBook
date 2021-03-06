# 深度学习基础知识

### 1.梯度消失和梯度爆炸

神经网络计算出损失函数对每个参数的梯度后，根据梯度去更新参数的值。但有的时候，随着梯度往前传，变得越来越小，导致前面层的参数基本不更新，算法不能收敛到一个比较好的结果，这种现象叫做梯度消失。

梯度爆炸则相反，随机梯度向前传播，梯度越来越大，参数变化剧烈，导致算法发散，这种现象叫做梯度爆炸，通常出现在循环神经网络中（为什么）。

### 2.参数初始化

偏置通常初始化为0或较小的数0.01

常见的参数（权重w）初始化方法：

（1） 随机初始化

正态分布初始化：均值为0，方差为1

均匀分布初始化

（2）Axiver初始化（Glorot初始化）

针对sigmoid、tanh激活函数

假设$fan_{in}$为某一层的输入数量，$fan_{out}$为输出数量

正态分布时：$\mu=0, \sigma^2=2/(fan_{in}+fan_{out})$

均匀分布时：[+r, -r]，其中$r=\sqrt{3 \sigma^2}=\sqrt{6/(fan_{in}+fan_{out})}$

（3）He初始化

针对Relu系列

正态分布时：$\mu=0, \sigma^2=2/fan_{in}$

均匀分布时：[+r, -r]，其中$r=\sqrt{3 \sigma^2}=\sqrt{6/fan_{in}}$

（4）Lecunn初始化

正态分布时：$\mu=0, \sigma^2=1/fan_{in}$

均匀分布时：[+r, -r]，其中$r=\sqrt{3 \sigma^2}=\sqrt{3/fan_{in}}$

**【问题】神经网络参数是否可以全部初始化为0？**

不可以

### 3.激活函数

**【问题】写出激活函数及其导数**

Sigmoid: $f(z) = \frac{1}{1+e^{-z}}$ 导数：$f(z)' = f(z)(1-f(z))$

Tanh：$f(z) = \frac{e^z-e^{-z}}{e^z+e^{-z}}$ 导数：$f(z)=1-f(z)^2$

Relu：$f(z) =max(0, z)$ 导数：$f(z)'=0, z\le0; f(z)'=1, z>0$

**【问题】为什么Sigmoid和Tanh激活函数会导致梯度消失现象？**



**【问题】ReLU激活函数的优点是什么？有什么局限性以及如何改进？**

* 优点

  计算更简单（不用计算e的指数次冪）

  不存在饱合现象，缓解了梯度消失问题

* 局限性

  存在神经元死亡的现象

* 改进

  PRelu

  

### 6.特征归一化

* 线性函数归一化（min-max 归一化）
  $$
  x_{norm} = \frac{x - x_{min}}{x_{max}-x_{min}}
  $$
  

* 零均值归一化
  $$
  x_{norm}=\frac{x-\mu}{\sigma}
  $$
  

$\mu$为均值，$\sigma$为标准差

**【问题】为什么要进行归一化？**

