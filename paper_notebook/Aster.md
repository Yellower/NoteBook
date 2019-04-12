> **论文：**Aster: An Attentional Scene Text Recognizer with Flexible Rectification
>
> 发表期刊：PAMI
>
> 发表时间：2018
>
> 编辑时间：2019.4.8

> Abstract—A challenging aspect of scene text recognition is to handle text with distortions or irregular layout. In particular, perspective text and curved text are common in natural scenes and are difficult to recognize. In this work, we introduce ASTER, an end-to-end neural network model that comprises a rectification network and a recognition network. The rectification network adaptively transforms an input image into a new one, rectifying the text in it. It is powered by a flexible Thin-Plate Spline transformation which handles a variety of text irregularities and is trained without human annotations. The recognition network is an attentional sequence-to-sequence model that predicts a character sequence directly from the rectified image. The whole model is trained end to end, requiring only
> images and their groundtruth text. Through extensive experiments, we verify the effectiveness of the rectification and demonstrate the state-of-the-art recognition performance of ASTER. Furthermore, we demonstrate that ASTER is a powerful component in end-to-end recognition systems, for its ability to enhance the detector.

场景文字识别中一个主要的挑战是处理变形的文本和不规则布局，特别是透视文本和弯曲（布局）文本。本文提出了一种新的网络，Aster，它利用矫正网络对送入的文本进行矫正，然后再用识别网络进行识别。同时，将两个网络整合成一个整体，采用端到端的训练方式训练，使得矫正网络的训练不需要额外的训练数据。

![](/home/laifeng/图片/2019-04-08 10-43-03 的屏幕截图.png)

## 模型

### 1.矫正网络

矫正网络利用2D变换对输入的图片进行矫正，其中，采用的变换是溥板样条插值（TPS）的方法。矫正网络主要包括三个部分：利用定位网络定位控制点、利用Grid Generator计算变换函数和采样网格（sampling grid）、利用Sampler进行插值处理。

矫正网络能处理的情况：

![](/home/laifeng/图片/2019-04-08 11-06-50 的屏幕截图.png)

矫正网络的结构：

![](/home/laifeng/图片/2019-04-08 11-06-16 的屏幕截图.png)

* 定位网络
* Grid Generator
* Sampler

