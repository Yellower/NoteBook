# YOLO论文阅读笔记

> 论文：You Only Look Once: Unified, Real-Time Object Detection
>
> 发表期刊：
>
> 发表时间：

### 1.摘要

* 将目标检测看作是对bounding boxes和类别概率的回归问题

* 整个检测过程使用一个网络，可以端到端的训练
* 检测速度快。YOLO网络可以达到45帧的速度，而Fast YOLO的速度可以达到155帧，但mAP值仍为其他实时检测算法的两倍

![YOLO框架](../.gitbook/assets/2019-07-15 21-20-30 的屏幕截图.png)

### 2.优缺点

**优点**

* 速度快

* 检测时考虑了整张图片

  不同于滑动窗口和候选框方法，YOLO检测时“看”到了整张图片，检测时考虑到了物体的上下文信息

* YOLO学习到了物体的通用表示

  当在自然图片中训练后，直接在艺术作品中测试，效果好于其他算法。作者的理由是在实际应用中很难保证测试的图片与训练图片保持同一分布。

**缺点**

* 精度不高

### 3. 网络模型

![](../.gitbook/assets/2019-07-17 19-54-16 的屏幕截图.png)

YOLO将输入图像划分为$S\times S$的栅格，每个栅格负责检测中心落在该栅格中的物体,如下图所示：

![](../.gitbook/assets/20170420213841466.png)

每个栅格预测$B$个bounding boxes和这些bounding boxes对就的confidence scores。

confidence scores表示该模型对于box的预测：该box包含物体的概率和box的坐标有多准。confidence定义为$Pr(Object)*IOU^{truth}_{pred}$，如果该单元格不包含物体，则confidence的值为0，否则让confidence等于predicted bounding box 与ground truth box的IOU。

每个边界框包含5个预测：$x,y,w,h, confidence$。其中，$(x,y)$代表预测的bounding box中心与栅格边界的相对值，高度和宽度为相对于整张图的高和宽的比例，confidence表示预测的bounding box与ground truth box的IOU值。

![](../.gitbook/assets/20170420213936232.gif)

每个栅格同时预测$C$个类条件概率$Pr(Class_i|Object)$。表示栅格在包含Object时，该Object为类别$i$的概率。且只为每个栅格预测一组类条件概率。

**注意**

类条件概率是针对每个栅格的，而confidence scores则是针对每个bounding box的。在测试阶段，将box的confidence和类条件概率相乘即可以得到box关于某个类别的物体的confidence:
$$
Pr(Class_i|Object)*Pr(Object)*IOU^{turth}_{pred}=Pr(Class_i)*IOU^{truth}_{pred}
$$
该confidence同时反应了box包含该类物体的概率和预测框位置的准确性。

![](../.gitbook/assets/20170420214036891.gif)

在PASCAL VOC数据集中，作者使用$S=7,B=2$，类别数为$C=20$,所以最后的输入张量为：$7\times 7\times 30$（即$S\times S\times(B*5+C)$）。

![](../.gitbook/assets/20170420214125579.gif)

### 4.网络结构

![](../.gitbook/assets/2019-07-17 20-54-16 的屏幕截图.png)



### 5.损失函数



### 6.非极大值抑制

* 目的

从一堆检测框中去除冗余的检测框，保留最好的一个。

* 方法

对于Bounding Box的列表B及其对应的置信度S,采用下面的计算方式：选择具有最大score的检测框M,将其从B集合中移除并加入到最终的检测结果D中.通常将B中剩余检测框中与M的IOU大于阈值Nt的框从B中移除.重复这个过程,直到B为空。
常用的阈值是 `0.3 ~ 0.5`。
其中用到排序,可以按照右下角的坐标排序或者面积排序,也可以按计算的得分或概率排序。

![](../.gitbook/assets/20170420214324204.gif)

参考：[深入理解目标检测与YOLO（从v1到v3）](https://blog.csdn.net/qq_39521554/article/details/80694512)