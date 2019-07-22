# TextSnake阅读笔记

[TOC]

> 论文：TextSnake: A Flexible Representation for Detectng Text of Arbitrary Shapes
>
> 发表期刊：ECCV
>
> 发表时间：2018
>
> 日期：2019.07.22

### 1. 摘要

在这篇文章之前，文字检测中常用轴对齐的矩形、旋转矩形和四边形来表示文本，但这些表示方式在表示弯曲的文本时有很大的局限性，不能很好的将文本框上。因此，本文提出了一种新的表示方式：TextSnake。TextSnake采用一系列重叠的圆盘(disk)来表示文本，每个圆盘带有两个几何属性：半径和角度，可以很容易的表示水平、旋转、弯曲的文本。利用这种新的表示文本的方式，作者提出了新的检测方法，在针对弯曲文本的检测数据集（Total-Text和SCUT-CTW1500）上取得了很好的效果。

### 2. 介绍

#### 2.1文本表示形式

主要有四种：轴对齐矩形、旋转矩形、四边形和TextSnake

![](../.gitbook/assets/2019-07-22 10-12-51 的屏幕截图.png)

#### 2.2主要贡献

1. 提出了一种灵活的、适用于任何形状的文本表示方式；
2. 基于这种表示方式提出了新检测算法；
3. 提出的检测算法在多个标准数据（包括水平、旋转、弯曲）上达到了世界领先水平

### 3.方法论

#### 3.1 表示

![](../.gitbook/assets/2019-07-22 10-30-20 的屏幕截图.png)

对于一个包含多个字符的文本$t$，可以将它表示为一个有序列表$S(t)$:
$$
S(t)=\{D_0,D_1,D_2,\dots,D_n\}
$$
其中，$D_i$表示第$i$个圆盘，$n$为圆盘总数。

每个圆盘表示为：$D=(c, r, \theta)$。半径$r$定义为文本$t$局部宽度的一半，$\theta$为点$c$处的切线方向。

**注意：圆盘数量不一定和字符数量相等。**

#### 3.2 流程

![](../.gitbook/assets/2019-07-22 10-54-29 的屏幕截图.png)

利用FCN网络预测得到热图，包括TCL（text center line）和TR（text regions）以及半径$r, cos\theta, sint\theta$等几何参数。对TCL进行TR的**mask操作??**得到Masked TCL，再利用`disjoint set`算法得到文本中心线实例分割结果。最后利用`striding algorithm`重建文本的检测框，得到最终的检测结果。
