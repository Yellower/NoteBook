# MobileNet阅读笔记

### 1. MobileNet V1

>论文：MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications
>
>发表期刊：CVPR
>
>发表时间：2017

为了让图像分类模型能够在手机等移动设备下快速运行，需要设计出计算量更小，但分类精度与其他网络模型相近的轻量级网络。本文作者针对卷积神经网络中的卷积层进行了修改，提出了深度可分离卷积（Depth-wish Separable Convolution）。

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

标准卷积与深度可分离卷积计算量对比：

![](F:\NoteBook\.gitbook\assets\mobilenetv1.png)

卷积核大小通常设置为$3 \times 3$，所以深度可分离卷积的计算量小了近8-9倍。

#### 1.2 深度可分离卷积层

使用中Depthwise Convolution和Point-wise Convolution层均使用BN和ReLU6。结构如图：

![](F:\NoteBook\.gitbook\assets\mobilenet_conv_bn_relu.png)

ReLU6的计算公式：
$$
ReLU6 = min(max(0, z), 6)
$$
**原因：个人理解**

主要是考虑到移动设备上使用的浮点数精度不高(float16)的原因，如果激活函数的输出$z$太大，假设使用浮点数$w$与$z$相乘，如果$w$因为精度丢失，则相乘结果差异较大，但是通常限制$z$的值在较小范围，则相乘结果基本不会因为精度丢失有太大浮动。

#### 1.3 网络结构

![](F:\NoteBook\.gitbook\assets\mobilenetv1_architecture.png)

#### 1.4 Width Multiplier 和 Resolution Multiplier

考虑到不同情况下，有时需要精度高的网络，有时需要速度快的网络，所以作者设置了两个参数，通过调节参数大小达到不同的目的。

* Width Multiplier $\alpha$

  每一层输入通道数$M$变成$\alpha M$，输出通道数$N$变成$\alpha N$。论文中设置的$\alpha \in \{1, 0.75, 0.5, 0.25\}$

  计算量为：
  $$
  D_K * D_K * \alpha M * D_F * D_F + \alpha M * \alpha N * D_F * D_F
  $$

* Resolution Multiplier $p$

  输入图片的大小变为原始大小的$p$倍，直接通过设置网络输入大小来实现，论文中使用的大小为{244, 192, 160, 128}

  则计算量为：
  $$
  D_K * D_K * \alpha M * p D_F * p D_F + \alpha M * \alpha N * p D_F * p D_F
  $$

### 2. MobileNet V2

> 论文：MobileNetV2: Inverted Residuals and Linear Bottlenecks
>
> 发表期刊：
>
> 发表时间：2018

**为什么改进MobileNetV1**

1.MobileNetV1的结构比较简单，仅仅是一些层的堆叠，而类似residual block这种的连接结构已经被证明是有效的，所以可以借鉴类似的结构；

2.

MobileNet v2在v1的基础上，采用了resnet网络中的residual block结构，主要有两个改进：

* 采用线性激活函数(residual block最后一层1x1卷积采用线性激活函数，论文里有证明)
* Inverted residual block

#### 2.1 Inverted Residual Block

Residual block与Inverted residual block模块对比：

![](F:\NoteBook\.gitbook\assets\inverted_residual_block.png)

不同点：

* $3 \times 3$卷积采用depth-wish convolution
* $3 \times 3$卷积输出深度为$t*input\_channel$，$t$为expantion ratio，大于1
* 输出时$1\times 1$卷积采用线性激活函数

**为什么中间的通道数要大？**

正常的residual block先经过$1*1$卷积降低通道数，再经过$3*3$卷积减少计算量，最后通过$1*1$卷积增加通道数，两头通道数大，中间小；而在invert residual block中，由于$3*3$卷积层采用的是深度卷积，计算量小，所以可以使用大的通道数，效果会更好，所以先用$1*1$卷积升高通道数，最后的$1*1$卷积降低通道数。

**为什么最后采用线性激活函数？**

Xception已经实验证明了 Depthwise 卷积后再加ReLU 效果会变差

Inverted residual block结构如下：

![](F:\NoteBook\.gitbook\assets\mobilenetv2_block_configuration.png)

![](F:\NoteBook\.gitbook\assets\mobilenetv2_two_block.png)

#### 2.2 网络结构

![](F:\NoteBook\.gitbook\assets\mobilenetv2_architecture.png)

#### 2.3 实验结果

![](F:\NoteBook\.gitbook\assets\mobilenetv2_result.png)

### 3. MobileNet V3

> 论文：Searching for MobileNetV3
>
> 发表期刊：
>
> 发表时间：2019

MobileNet v3主要进行了三方面的改进：

* 升级了MobileNet v2中的building block(residual block)
* 利用搜索方式查找最佳的网络结构
* 使用h-swish代替ReLU

#### 1.1 Building Block

MobileNet v2与MobileNet V3模块对比：

![](F:\NoteBook\.gitbook\assets\mobilenetv3_block-1576806441453.png)

主要不同时加入了Sequeeze-and Excite（SE）模块，参考了MnasNet中SE模块的使用。

#### 3.2 h-swish

swish激活函数效果要好于ReLU，所以作者希望利用s-wish代替ReLU

swish激活函数公式：
$$
swish x=x*\sigma(x)
$$
其中$\sigma$为sigmoid激活函数。

可见，swish激活函数包含e的指数冪计算，对移动设备来说计算难度较大。

作者将sigmoid函数替换成$\frac{ReLU6(x+3)}{6}$，得到swish的近似版本h-swish，同时大大减小计算难度:
$$
h\_swish = x\frac{ReLU6(x+3)}{6}
$$
![](F:\NoteBook\.gitbook\assets\swish.png)

#### 3.3 网络结构

作者利用搜索的方法，针对不同应用场景得到了两种网络结构MobileNet V3-Large和MobileNet V3-Small

![](F:\NoteBook\.gitbook\assets\mobilenetv3_large.png)

![](F:\NoteBook\.gitbook\assets\mobilenetv3_small.png)

#### 3.4 实验结果

![](F:\NoteBook\.gitbook\assets\mobilenetv3_result.png)