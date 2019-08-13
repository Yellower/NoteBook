# 滑动平均模型

参考文章：[深入解析TensorFlow中滑动平均模型与代码实现](https://blog.csdn.net/m0_38106113/article/details/81542863)

先记录一下，有时间再整理

### 1. 介绍

Tensorflow中的滑动平均模型使用的是滑动平均算法，又称指数加权移动平均算法。它可以用来估计变量的局部平均值，使变量的更新与一段时间内的取值有关。

假设有一个变量$v$，在时刻$t$记为$v_t$，$\theta_t$为变量$v$在$t$时刻的取值，也就是说不使用滑动平均模型时$v_t = \theta_t$。但是在滑动平均模型中，定义了新的更新公式：
$$
w_t=w_{t-1}*\beta + \theta_t*(1-\beta)
$$
$w_t$称为变量$v_t$的滑动平均值。在滑动平均模型中希望用$w_t$代替$v_t$。

**为什么使用滑动平均模型?**

在深度学习中，变量直接使用梯度进行更新，如果直接使用当前的取值，变量的值的变化曲线不是平滑的，而会波动的。如果使用滑动平均模型，就可以抑制值的跳变，使得整体数据变得更加平滑——这意味着数据的噪音会更少，而且不会出现异常值。

### 2. Tensorflow中的使用

```python
#1. 定义一个滑动平均的类
global_step = tf.get_variable('global_step', [], initializer=tf.constant_initializer(0), trainable=False)
variable_averages = tf.train.ExponentialMovingAverage(decay, global_step)
# 2. 获得需要计算滑动平均值的变量
variables_to_average = tf.trainable_variables()
# 3. 创建滑动平均op
variables_averages_op = variable_averages.apply(variables_to_average)
# 4. 和参数更新操作绑定
train_op = tf.group(apply_gradient_op, variables_averages_op)
```

训练时使用变量原始值进行训练，而测试时使用变量的滑动平均值进行预测，可以提高模型的鲁棒性。

### 3.从保存的模型中载入滑动平均值

```python
#调用滑动平均类的variables_to_restore函数生成变量重命名字典
```

