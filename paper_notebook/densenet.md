# DenseNet论文阅读

> 论文：Densely Connected Convolution Network
>
> 发表期刊：CVPR
>
> 发表时间：2017

### 1. dense连接结构

![](F:\NoteBook\.gitbook\assets\DenseNet1.png)

每一层输入都是之前所有层的输出特征图的拼接，拼接方法为concat

每一层不是只有单独的卷积层，作者参照Resnet结构，每一个conv block包含BN-Relu-Conv结构

每一层的输出特征图深度均为K，作者称之为growth rate，这样输入的深度会随层数增加一层而增加K，K通常设置为12、32大小

### 2. 网络结构

![](F:\NoteBook\.gitbook\assets\DenseNet2.png)

如果整个网络都采用Dense连接，那么越靠后的卷积层输入深度会越大，这意味着越靠后的层参数越多。为了避免这种情况，作者采用Dense Block的形式，每一个Block内部采用Dense连接方式，每两个Block之间增加Transition Block将两个Dense Block连接。

同时，为了尽一步减小卷积层输入的深度，作者还进行一些改进。

* 对Conv Block改进。作者发现很多其他研究人员使用$1*1$的卷积核来减小深度，这种方法称为bottleneck结构。具体方法：对输入先进行$1*1$的卷积，将大的输入深度减小，然后再进行$3*3$的卷积，这样$3*3$的卷积参数会减小很多，所以Conv Block为BN-Relu-Conv(1 * 1)-BN-Relu-Conv(3 * 3)作者将使用这种结构的DenseNet命名为DenseNet-B。
* 对Transition Block改进。如果Dense Block包含$m$个特征图（即Dense Block的输入特征图和该Block内所有Conv Block的输出的拼接），则Transition Block的卷积输出深度为 $int(\theta m)$（int表示向下取整）。通常设置$\theta=0.5$。

论文中作者针对不同的数据集设计了不同的网络，整体结构相似，以下是ImageNet数据集上使用的几种结构。

![](F:\NoteBook\.gitbook\assets\DenseNet3.png)

针对Cifar10、Cifar100数据集，网络的配置为：使用网络深度$L$和Growth Rate$K$表示不同的结构，如$(L=40, K=12)$。采用3个一样的Dense Block，每一个Block包含$(L-4)/3$层卷积层。初始时只有一层$3*3, s=1$的卷积，不缩小图片尺寸。其他结构与ImageNet一样。

### 3. 总结

DenseNet优点：

* 参数少。特征图深度相比于其他网络减小很多，所以整体网络参数小。
* 将前面的输出连接到后面的层。前向过程保留了前面层的信息，反向过程中梯度可以直接传递到前面层，缓解梯度消失问题。
* 精度高于之前的网络。