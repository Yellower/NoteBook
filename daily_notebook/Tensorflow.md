# Tensorflow系列

### 1. `tf.nn.sparse_softmax_cross_entropy_with_logits()`

```python
tf.nn.sparse_softmax_cross_entropy_with_logits(
    _sentinel=None,
    labels=None,
    logits=None,
    name=None
)
```

`logits`为神经网络输出层的输出，shape为`[batch_size, num_classes]`；`labels`为标签，shape为`[batch_size]`。

内部原理：（参考[[TensorFlow]关于tf.nn.sparse_softmax_cross_entropy_with_logits(](https://blog.csdn.net/ZJRN1027/article/details/80199248))

1. 对`logits`进行`softmax`操作
2. 对`labels`进行`one-hot`编码
3. 计算cross-entropy

如果`labels`是`one-hot`编码格式，可以使用`tf.nn.softmax_cross_entropy_with_logits()`计算cross-entropy损失。

### 2. `tf.identity()`

```python
tf.identity(
    input,
    name=None
)
```

Return a tensor with the same shape and contents as input.

### 3.`tf.get_local_variable()`

```python
tf.get_local_variable(
    name,
    shape=None,
    dtype=None,
    initializer=None,
    regularizer=None,
    trainable=False,
    collections=None,
    caching_device=None,
    partitioner=None,
    validate_shape=True,
    use_resource=None,
    custom_getter=None,
    constraint=None,
    synchronization=tf.VariableSynchronization.AUTO,
    aggregation=tf.VariableAggregation.NONE
)
```

Behavior is the same as in `get_variable`, except that variables are added to the `LOCAL_VARIABLES` collection and `trainable` is set to `False`. This function prefixes the name with the current variable scope and performs reuse checks.

### 4. `tf.name_scope()`和`tf.variable_scope()`

参考：[tf.name_scope()和tf.variable_scope()](https://blog.csdn.net/weixin_38698649/article/details/80099822)

**区别**

对于`tf.Variable()`方式创建变量，具有相同效果，都会在变量名称前面加上作用域名称

对于`tf.get_variable()`方式创建变量时，只有｀｀`variable_scope()`作用域下的域名会加到变量名称前面

**例子**

```python
#tf.name_scope()
with tf.name_scope("my_name_scope"):
    v1 = tf.get_variable("var1", [1], dtype=tf.float32) 
    v2 = tf.Variable(1, name="var2", dtype=tf.float32)
    a = tf.add(v1, v2)
    print(v1.name)
    print(v2.name) 
    print(a.name)
#输出
var1:0
my_name_scope/var2:0
my_name_scope/Add:0

#tf.variable_scope()
with tf.variable_scope("my_variable_scope"):
    v1 = tf.get_variable("var1", [1], dtype=tf.float32)
    v2 = tf.Variable(1, name="var2", dtype=tf.float32)
    a = tf.add(v1, v2)
    print(v1.name) 
    print(v2.name)
    print(a.name)
#输出
my_variable_scope/var1:0
my_variable_scope/var2:0
my_variable_scope/Add:0
```

### 5. `tf.get_collection()`

```python
tf.get_collection(
    key,
    scope=None
)
```

获得`scope`下的`key`对应的集合中的变量

```python
losses = tf.get_collection(tf.GraphKeys.LOSSES, clone_scope)#clone_scope是一个name_scope作用域
total_loss = tf.add_n(losses)
```

### 6. `tf.reduce_mean()`

获得张量在某维度上均值

函数说明：

```python
tf.compat.v2.reduce_mean(
    input_tensor,
    axis=None,    
    keepdims=False,  #False维度减1，True时保存张量shape不变
    name=None
)
```

```python
x = tf.constant([[1., 1.], [2., 2.]])
tf.reduce_mean(x)  # 1.5
tf.reduce_mean(x, 0)  # [1.5, 1.5]
tf.reduce_mean(x, 1)  # [1.,  2.]
tf.reduce_mean(x, [0,1]) # 1.5
```

### 7.获取张量的尺寸 

```python
# tf.shape() 方法
>>> tf.shape(v)
<tf.Tensor 'Shape:0' shape=(2,) dtype=int32>
 
# shape 属性
>>> v.shape
TensorShape([Dimension(100), Dimension(100)])
 
# get_shape() 方法
>>> v.get_shape()
TensorShape([Dimension(100), Dimension(100)])

#as_list()
>>>v.shape.as_list()   #[100, 100]
>>>v.get_shape().as_list()   #[100, 100]
```

### 8.rnn实现

```python
############实现多层LSTM############
#定义循环体
cell = tf.nn.rnn.MultiRNNCell([
tf.nn.rnn_cell.BasicLSTMCell(HIDDEN_SIZE) for _ in range(NUM_LAYERS)
])

outputs, _ = tf.nn.dynamic_rnn(cell, input, dytpe=tf.float32) #outputs是顶层LSTM在每一步的输出，维度为[batch_size, time, HIDDEN_SIZE]

############实现双向LSTM################
cell_fw = tf.nn.rnn_cell.BasicLSTMCell(HIDDEN_SIZE)
cell_bw = tf.nn.rnn_cell.BasicLSTMCell(HIDDEN_SIZE)

outputs, _ = tf.nn.bidirectional_dynamic_rnn(cell_fw, cell_bw, input)#outputs是（output_fw, output_bw）的元组，每一个的维度与前面类似
```

### 9.上采样

* resize

```python
input_shape = tf.shape(input_data)
output = tf.image.resize_nearest_neighbor(input_data, (input_shape[1] * 2, input_shape[2] * 2))
```

* 反卷积

返回卷积操作的输入结果，`filters`为输出深度，`kernel_size`卷积核尺寸

```python
tf.layers.conv2d_transpose(
    inputs,
    filters,
    kernel_size,
    strides=(1, 1),
    padding='valid',
    data_format='channels_last',
    activation=None,
    use_bias=True,
    kernel_initializer=None,
    bias_initializer=tf.zeros_initializer(),
    kernel_regularizer=None,
    bias_regularizer=None,
    activity_regularizer=None,
    kernel_constraint=None,
    bias_constraint=None,
    trainable=True,
    name=None,
    reuse=None
)

#示例：上采样2倍，即输出为输入尺寸的2倍
output = tf.layers.conv2d_transpose(input_data, numm_filter, kernel_size=2, padding='same',
                                            strides=(2,2), kernel_initializer=tf.random_normal_initializer())
#从正向卷积看输出为输入的1／2，所以反卷积输出为输入的2倍
#tensorflow中卷积输出大小：padding='same' out = ceil(in / stride); padding='valid' out = ceil((in - k_size + 1)/stride
```



