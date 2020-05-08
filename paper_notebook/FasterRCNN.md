# Faster RCNN论文阅读

Faster RCNN的主要特点是：使用RPN(Region Proposal Network)生成候选框，而不是采用Selective Search

整体的结构如下：

![](F:\NoteBook\.gitbook\assets\Faster Rcnn.png)

首先，将输入图片送给卷积网络得到特征图，在特征图上使用RPN网络回归出候选检测框，由于候选框的大小是不一致的，所以利用ROIPooling层根据候选框大小和特征图，得到统一大小的分类特征图（7*7），对分类特征图进行分类和回归，得到最终的检测框和目标类别。

### 1.RPN

![](F:\NoteBook\.gitbook\assets\RPN.png)

在特征图的每个位置上，使用$3*3$的滑窗进行滑动，将滑窗内的特征图转成256-d的特征向量，再做分类和回归。具体的方法：使用3*3的卷积在特征图上滑动，卷积输入特征图深度为256（VGG为512），然后回归和分类两个分支分别使用1\*1的卷积得到各自的输出。其中，分类输出为$2k$ ，回归的输出为$4k$，K是在每个位置上的anchor的数量（通常为9，三种尺寸的anchor，每种有三个长宽比）。

**损失计算**

标签生成如下：

1.正样本：（1）如果anchor与Ground Truth的IOU大于0.7，anchor设为正样本；（2）与某一Ground Truth的IOU最大的anchor，设为正样本

2.负样本：与所有Ground Truth的IOU均小于0.3

不是正样本，也不是负样本的anchor不参与损失计算

采用多任务损失：

![](F:\NoteBook\.gitbook\assets\RPN_loss.png)

回归坐标计算：

![](F:\NoteBook\.gitbook\assets\RPN_reg.png)

### 2.训练

采用迭代训练的方式

**4步迭代训练**

1.骨架网络转入ImageNet预训练权重，微调RPN；

2.训练另一个网络，骨架网络载入ImageNet预训练权重，使用第一步的RPN生成候选框，训练Fast RCNN；

3.将第2步的骨架网络替换RPN的骨架网络，实现骨架网络的共享，固定骨架网络，训练RPN；

4.固定骨架网络，训练Fast RCNN；