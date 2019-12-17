# SENet论文阅读

> 论文：Squeeze-and-Excitation Networks
>
> 发表期刊：
>
> 发表时间：2018
>

在CNN中，卷积层通过卷积核来学习空间上和通道上的特征，得到特征图。之前的研究大部分集中在空间上的研究，比如Inception Net。本文作者希望捕捉输出特征图中每个通道之间的依赖关系，对包含有用信息的通道赋予较大的权重，对无用的通道赋予较小的权重，提高网络的学习能力。因此，作者提出了SE Block。

### 1.SE Block

SE Block结构如图：

![](F:\NoteBook\.gitbook\assets\SEBlock.png)

SE Block，全称Sequeeze-and-Excitation Block。Block主要分为两块：

* Sequeeze

  卷积的输出大小通常为$H\times W \times C$，因此特征图上每个通道均为$H \times W$大小。为了估计通道之间的依赖关系，需要对每个通道计算它对应的全局特征。因此，作者采用Global Average Pooling层来获取每个通道的全局特征，输出大小为$1*C$。对应图中的$F_{sq}$

* Excitation

  为了计算通道间的依赖关系，作者利用FC-Relu-FC-Sigmoid的结构。其中使用两层FC是为了减少参数，第一层FC将输入映射到更小的维度（$C/r$，$r$收缩系数），再利用FC映射到$C$维度。对应图中$F_{ex}$

最后，将原始的输出特征图与SE Block的输出权重按通道相乘即可。对应图中$F_{scale}$

### 2. SE Net

SE Block是一个通用的结构，可以很容易的嵌入到经典的CNN模型中，比如InceptionNet或ResNet。

在Inception模块和Res模块加入SE Block后的结构如图：

![](F:\NoteBook\.gitbook\assets\SENet_2.png)

原文给出的SE-ResNet和SE-ResNeXt的结构如图：

![](F:\NoteBook\.gitbook\assets\SENet_3.png)

其中，$fc, [16, 256]$表示两层全连接层，输出单元分别为16和256。

