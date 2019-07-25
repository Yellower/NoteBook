## Multi-Oriented Scene Text Detection via Corner Localization and Region Segmentation阅读笔记

> 论文：Multi-Oriented Scene Text Detection via Corner Localization and Region Segmentation
>
> 发表期刊：CVPR
>
> 发表时间：2018

### 1.介绍

当前基于深度学习的文字检测算法大致分为两类：

1）采用通用目标检测的方法，定位场景中的文本，并回归文本的边界框

难点：文本的随机旋转角度和大的长宽比造成检测困难

2）直接分割出文本区域

难点：需要复杂的后处理

本文在些基础上，提出了结合以上两种方法的文字检测算法，不仅能够处理长的、具有旋转角度的文本，而且不需要复杂的后处理。

#### 1.1 场景文字检测具有挑战性

* 内因
* 外因

#### 1.2 算法流程

![Corner points and position-sensitive maps](../.gitbook/assets/2019-07-25 19-56-24 的屏幕截图.png)

![算法流程](../.gitbook/assets/2019-07-25 19-56-37 的屏幕截图.png)

流程如下：

1) 检测输入图片的角点（corner points，左上、右上、右下、左下），同时得到图片的位置敏感图（position-sensitive maps，不是文本/非文本分类图, text/non-txt map）

2) 通过采样(sampling)和组合(grouping)角点得到候选框

3) 利用位置敏感图为候选框评分

4) 非极大值抑制算法(NMS)筛选出最终的检测框