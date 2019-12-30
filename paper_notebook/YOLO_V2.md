# YOLO V2论文阅读

>论文：YOLO9000: Better, Faster, Stronger
>
>发表期刊：CVPR
>
>发表时间：2017

### 1.介绍

* YOLO V2

  yolo v1网络虽然检测速度快，但相比于其他检测网络，mAP较低，所以作者对yolo v1进行各种改进，在保证检测速度基本不变的情况下，大幅提升了mAP值。在67 FPS下，YOLOv2在VOC2007数据集上达到了76.8 mAP。在40 FPS下，YOLOv2 达到了78.6的mAP值，比当时最好的Faster rcnn和SSD网络检测效果更好，同时速度也比它们快。

  ![](F:\NoteBook\.gitbook\assets\yolov2_acc_compare.png)

* YOLO 9000

  作者想要搭建一个网络能检测特别多类别的物体，但目前的检测数据集中类别不多，同时目标检测数据集制作成本高，而分类数据集中的类别数比较多，而且制作成本低。因此，作者提出混合目标检测数据集和分类数据集，并提出一种在混合后数据集上训练的方法，最终训练出了YOLO9000。YOLO9000能检测9000个类别的目标，同时达到了实时的检测速度。

### 2.YOLO v2改进

![](F:\NoteBook\.gitbook\assets\yolov2_改进.png)

* batch norm

  为每层卷积层加上BN层，加快了收敛速度，同时带来了正则化效果。

* hi-res classifier

  训练检测模型时，都使用ImageNet上预训练的backbone网络。但是，预训练分类网络输入为224*224，而检测网络输入大小为448\*448，导致训练检测网络时，网络还需要学习从224大小过渡到448大小。因此，作者将backbone网络先在448\*448大小上进行微调，将微调后的模型作为预训练模型。

* convolutional

  去除了YOLOv1中的全连接层，网络变成全卷积网络，这样可以适应不同尺寸的输入。同时，将输入大小改为416*416，输出大小改为13\*13，增加了预测bbox数量。

* anchor boxes

  学习了Faster rcnn中的方法，为特征图的每个位置设置多个anchors，对anchors进行回归。

* new network

  使用darknet-19网络作为骨架网络。darknet-19网络计算量小，同时分类精度与VGG16等相差不大。

  使用BN层。

  ![](F:\NoteBook\.gitbook\assets\darknet19.png)

* dimension priors

  其他网络在使用anchor boxes时，一般是手动设置，作者觉得如果一开始设置的anchor较好时，也会对学习有利。因此提出了用k-means聚类得到anchors。

* location prediction

  在RPN(region proposal network)中，通过回归与anchors之间的offset，生成proposal检测框。作者觉得这种方式导致了训练不稳定，因此，采用直接回归坐标的方式。

  假设网络对每个bounding box预测的坐标为$t_x, t_y, t_w, t_h, t_o$，特征图中某一单元格距图片左上角的坐标为$(c_x, c_y)$，先验框（anchor）的宽高为$p_w, p_h$，则：

  ![](F:\NoteBook\.gitbook\assets\yolov2_coor.png)

  ![](F:\NoteBook\.gitbook\assets\yolov2_coor_calculation.png)

* passthrough

  

* multi-scale

  由于网络只含有卷积层、池化层，所以输入大小可以任意。假设只训练某一个尺寸，则对其他尺寸的输入检测效果并不会最佳，因此，作者设置了多个输入尺寸，每训练几个batch就切换输入尺寸，称为多尺度训练。

* hi-res detector

  因为网络是通过多尺度训练方式得到的，所以网络能适应不同大小的输入。在要求速度快的情况下，使用小输入，在要求精度高的情况下，使用大输入。在使用高分辨率的输入的情况下，精度要高得多。

### 3.实验结果 

voc数据集上的结果：

在输入图片大小为544*544时，mAP值超过了Faster RCNN和SSD，同时保持了40FPS。

![](F:\NoteBook\.gitbook\assets\yolov2_result.png)

COCO数据集上的结果：

![](F:\NoteBook\.gitbook\assets\yolov2_result_coco.png)

