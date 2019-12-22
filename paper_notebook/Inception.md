# InceptionNet论文阅读

### 1.Inception v1

>论文：Going deeper with convolutions
>
>发表期刊：CVPR
>
>发表时间：2015

IncepthinNet，又称GoogleLeNet，是ILSCVR‘14的冠军网络（VGG第二）。与VGG等其他网络不同，Inception v1网络主要有两个特点：

* 采用堆叠Inception block的形式，而不是堆叠卷积、池化层
* block中采用多条支路并行的方式

#### 1.1 Inception Block

为了提高网络的效果，最直接的方法就是增加网络的尺寸，通过加深网络层数和每层采用更多的神经元。但这样做存在两个缺点：

* 网络参数大量增加，容易过拟合
* 计算量剧增，计算资源不满足

因此，需要从全连接方式转换到稀疏连接方式。基于此，作者采用多条卷积支路并行的方式。

Inception Block的结构如图：

![](F:\NoteBook\.gitbook\assets\Inceptionnet_v1_inception_block.png)

左边为原始的想法，但是$3 \times 3$ $5\times5$卷积参数多，因此在这两个卷积层前加入$1 \times 1$卷积，缩小输入深度，减小参数量，得到了右图的Inception Block。

#### 1.2 网络结构

![](F:\NoteBook\.gitbook\assets\Inceptionnet_v1_architecture.png)

$\#3 \times 3 reduce$、$\# 5 \times 5reduce$分别表示$3 \times 3$、$5 \times 5$卷积之前的$1 \times 1$卷积的输出深度。

#### 1.3 实验结果

![](F:\NoteBook\.gitbook\assets\Inceptionnet_v1_result.png)

#### 2. Inception V4

