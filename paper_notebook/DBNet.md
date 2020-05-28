# DBNet论文阅读

> 论文：Shape Robust Text Detection with Progressive Scale Expansion Network
>
> 发表期刊：
>
> 发表时间：2019

### 1.介绍

#### 1.1为什么提出DBNet?

目前主流的检测方法都基于分割来实现的，因为分割的方法能处理任意形状的文本行。而这些方法都需要复杂的后处理操作，来将预测的像素级特征图转化为文本区域或实例，极大增加了模型的推理时间。所以，作者提出Differentiable Binarization，并将它变成网络的一个模块，在加快推理时间的同时，提高了模型的效果。

主要思路是：一般的后处理包括两步（1）固定阈值的二值化处理（2）启发式方法得到文本实例或者CC。作者觉得固定的阈值不好，可以改成自适应的，即每个像素点采用不同的阈值，因此作者提出了Differentiable Binarization，利用网络预测阈值图。作者将二值化的模块插入到网络中去学习怎么预测阈值图。同时，可以不需要复杂的启发示后处理方法就可以得到文本实例，因此，模型的推理速度有很大提升。

### 2.网络

DBNet网络结构如下：

![](F:\NoteBook\.gitbook\assets\DBNet网络结构.png)

提出的DB的公式和曲线如下：

![](F:\NoteBook\.gitbook\assets\DB.png)

![](F:\NoteBook\.gitbook\assets\DB_曲线.png)

### 3.实验

在ICDAR2015上的结果如下：

![](F:\NoteBook\.gitbook\assets\DB_IC15.png)

