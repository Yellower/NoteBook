# YOLOV3论文阅读

YOLO V3相比于V2在COCO数据集上的精度结果有了很大的提升，但网络结构上总体的改进并不多，主要有以下几部分：

### 1.检测框的预测

检测框仍然预测三部分：

* 检测框坐标

采用与V2中相同的方式进行预测，其中anchors生成也采用K-means方法聚类得到。

网络的预测输出值为$t_x,t_y,t_w,t_h$，预测的检测框坐标为$b_x,b_y,b_w,b_h$。损失采用平方误差损失。

![](../.gitbook\assets\yolov3_coor-1577688940695.png)

* Objectness score

为每个检测框预测一个是否是物体的得分值。采用逻辑回归预测方式（采用二元交叉熵损失）。

* 类别概率

损失采用二元交叉熵损失，考虑到数据集中存在很多标签重叠的现象。

### 2. 多尺度预测

YOLO V3采用三个分支的形式，分别输出不同尺度的特征图，在三个特征图上都进行预测，

### 3. 骨架网络

将YOLO V2中的Darknet19更换成了特征提取能力更强的Darknet53，作为YOLOV3的骨架网络。

Darknet53的网络结构如下：

![](../.gitbook\assets\darknet53.png)

与Darknet19、Resnet网络的比较：

![](../.gitbook\assets\darknet53_acc-1577689060001.png)

### 4. 结果

![](../.gitbook\assets\yolov3_result.png)

**COCO数据集评估指标说明**

$AP$: 在IOU=.5:.05:.95时的AP平均值

$AP_{50}$: 在IOU=.5时的AP值

$AP_{75}$: 在IOU=.75时的AP值

$AP_S$: 小目标的AP值，$area<32^2$

$AP_M$: 小目标的AP值，$32^2<area<96^2$

$AP_L$: 小目标的AP值，$area>96^2$

这里$AP$都指的是$mAP$

![](../.gitbook\assets\yolov3_curve_AP.png)

![yolov3_mAP_curve_AP50](../.gitbook\assets\yolov3_mAP_curve_AP50.png)

