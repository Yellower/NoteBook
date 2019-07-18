# 输出特征图尺寸计算

### 1.通用计算方法

* 卷积层

  定义如下：

  O＝输出图像的尺寸

  I＝输入图像的尺寸

  K＝卷积核的尺寸

  N＝核数量

  S＝移动步长

  P＝填充数

  输出图像尺寸的计算公式为：
  $$
  O=\frac{I-K+2P}{S}+1
  $$

* 池化层

  定义如下：

  O＝输出图像的尺寸

  I＝输入图像的尺寸

  S＝移动步长

  $P_s$＝池化层的尺寸

  输出图像尺寸的计算公式为：
  $$
  O=\frac{I-P_s}{S}+1
  $$

### 2. Tensorflow中的计算方式

#### 2.1 padding="SAME"

卷积后的特征图大小跟卷积核大小无关，只跟卷积所用的步长有关，当卷积步长是1时，卷积前后特征图大小保持不变，自动对图像边界进行填充。

卷积、池化层计算大小公式为：

```python
out_height=ceil(input_height/stride[1]) #ceil()为向上取整
out_width =ceil(input_weight/stride[2])
```

#### 2.2 padding="VALID"

根据图片实际大小执行卷积，不对图像边界填充，一般卷积后特征图变小。

卷积、池化层计算大小公式为：

```python
out_height = ceil(float(in_height - filter_height + 1) / float(strides[1]))
out_width = ceil(float(in_width - filter_width + 1) / float(strides[2]))  
```

所以，通常使用`tf.pad()`对输入图像进行padding操作后再使用`VALID`卷积。



