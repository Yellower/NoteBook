# MobileNet阅读笔记

### 1. MobileNet V1

为了让图像分类模型能够在手机等移动设备的硬件下快速运行，需要设计出计算量更小，但分类精度与其他网络模型相近的网络。作者针对卷积神经网络中的卷积层进行了修改，提出了深度可分离卷积（Depth-wish Separable Convolution）。

#### 1.1 标准卷积层与深度可分离卷积层

卷积层与深度可分离卷积层对比：

![](F:\NoteBook\.gitbook\assets\depthwise_separable_convolution.png)

深度可分离卷积层将卷积操作拆分成两步：Depth-wish Convolution和 Point-wise Convolution。Depth-wise Convolution对输入的每个通道进行卷积，每个通道卷积输出深度为1，整个卷积层的输出深度等于输入深度。Point-wise Convolution使用$1 \times 1$的卷积核，输出深度为目标深度，可以理解为对Depth-wish convolution输出的每个通道的结果进行线性组合。

假设卷积层输入$D_F \times D_F \times M$，输出特征图大小$D_F \times D_F \times N$，标准的卷积核大小为$D_K \times D_k \times M \times N$，可以计算出标准卷积层的计算量为（每次卷积操作的计算量*卷积操作次数）：
$$
D_K * D_K * M * N * D_F * D_F
$$
在深度可分离卷积层中，Depth-wise Convolution的卷积核大小$D_K \times D_K \times M$，其中第$m$个卷积核对第$m$个通道进行卷积。计算量为：
$$
D_K * D_K * M * D_F * D_F
$$
Point-wist Convolution卷积核大小为$1 \times 1 \times M \times N$。整个深度可分离卷积层的计算量为：
$$
D_K * D_K * M * D_F * D_F + M * N * D_F * D_F
$$

#### 1.2 深度可分离卷积层

使用中Depthwise Convolution和Point-wise Convolution层均使用BN和ReLU6。结构如图：

![](F:\NoteBook\.gitbook\assets\mobilenet_conv_bn_relu.png)

ReLU6的计算公式：
$$
ReLU6 = min(max(0, z), 6)
$$
** 原因：个人理解**

主要是考虑到移动设备上使用的浮点数精度不高(float16)的原因，如果激活函数的输出$z$太大，假设使用浮点数$w$与$z$相乘，如果$w$因为精度丢失，则相乘结果差异较大，但是通常限制$z$的值在较小范围，则相乘结果基本不会因为精度丢失有太大浮动。

#### 1.3 网络结构

![](F:\NoteBook\.gitbook\assets\mobilenetv1_architecture.png)

#### 1.4 Width Multiplier 和 Resolution Multiplier

考虑到不同情况下，有时需要精度高的网络，有时需要速度快的网络，所以作者设置了两个参数，通过调节参数大小达到不同的目的。

* Width Multiplier $\alpha$

  每一层输入通道数$M$变成$\alpha M$，输出通道数$N$变成$\alpha N$.

  计算量为：
  $$
  D_K * D_K * \alpha M * D_F * D_F + \alpha M * \alpha N * D_F * D_F
  $$

* Resolution Multiplier $p$

  输入图片的大小变为原始大小的$p$倍

  则计算量为：
  $$
  D_K * D_K * \alpha M * p D_F * p D_F + \alpha M * \alpha N * p D_F * p D_F
  $$

### 2. MobileNet V2



### 3. MobileNet V3